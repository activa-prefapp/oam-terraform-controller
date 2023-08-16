# AWS-SSO custom module recommendations

## Limitations

The module defined in https://github.com/prefapp/tfm/tree/main/modules/aws-sso cannot be integrated without modification as it depends on the absolute path of a .yml file. When importing the module into OAM we do not have a storage medium.

```
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
|            NAME            |                            DESCRIPTION                             |                           TYPE                            | REQUIRED | DEFAULT |
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
| data_file                  | absolute path of the data to use (in YAML format).                 | string                                                    | true     |         |
```

You can review the complete import of the actual module [here](custom-modules.md).

## Recommendations

Understanding the above, the solution is to import the .yml file from a url or a private storage, modifying the module to establish a new variable, url or backet connection.


Assuming that a s3 backet can be a correct solution, the following modifications could be tried:

1. variables.tf:

```
variable "data_file" {
  type        = string
  description = "S3 URI of the data to use (in YAML format)"
}

variable "s3_bucket_name" {
  type        = string
  description = "Name of the S3 bucket containing the YAML file"
}

variable "s3_object_key" {
  type        = string
  description = "Object key of the YAML file in the S3 bucket"
}

variable "identity_store_arn" {
  type        = string
  description = "the arn of the SSO instance"
}

variable "store_id" {
   type        = string
   description = "the Identity store id"
}

```

2. main.tf:


```
locals {
  # ...
}

# ...

resource "aws_s3_bucket_object" "yaml_file" {
  bucket = var.s3_bucket_name
  key    = var.s3_object_key
}

locals {
  yaml = yamldecode(aws_s3_bucket_object.yaml_file.body)
}

# ...

```

3. permissions.tf:

```
locals {
  # ...
}

# ...

resource "aws_ssoadmin_permission_set" "permissions" {
  # ...

  depends_on = [
    aws_identitystore_group.groups,
    time_sleep.wait_30seg,
    aws_s3_bucket_object.yaml_file  # Add dependency on the S3 object
  ]
}

# ...

```

4. attachments.tf:

```
locals {
  # ...
}

# ...

resource "aws_ssoadmin_account_assignment" "accounts-permissions-groups" {
  # ...

  depends_on = [
    aws_ssoadmin_permission_set.permissions,
    aws_s3_bucket_object.yaml_file  # Add dependency on the S3 object
  ]
}

resource "aws_ssoadmin_account_assignment" "accounts-permissions-users" {
  # ...

  depends_on = [
    aws_ssoadmin_permission_set.permissions,
    aws_s3_bucket_object.yaml_file  # Add dependency on the S3 object
  ]
}

# ...


```

Once the modifications have been made and the suitability of the new code has been tested, the new module should be imported into the environment with a new OAM ComponentDefinition as done [here](custom-modules.md).

Define the new OAM application for the new provisioning with the new module:

```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: aws-sso-sample
  namespace: default
spec:
  components:
    - name: activaprefapp-sample-sso
      type: aws-sso-prefapp
      properties:
        data_file        = "s3://${var.s3_bucket_name}/${var.s3_object_key}"
        s3_bucket_name   = "your-bucket-name"
        s3_object_key    = "path/to/your/yaml/file.yaml"
        identity_store_arn = "your-identity-store-arn"
        store_id         = "your-identity-store-id"
        writeConnectionSecretToRef:
          name: aws-sso-conn   #required
        providerRef:
          name: aws   #if not specified the default value is 'aws'
```


Now you would be ready, if everything is configured correctly, to use your custom Terraform module and configure your SSO environment with the following command:

```
vela up -f path/aws-sso.yml
```


*[Back to contents](../README.md)*
