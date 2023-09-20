# Custom module integration

You can add your custom modules by configuring a new CRD for each module.

In this demo a new ComponentDefinition is generated to add the ability to provision a custom module. The module to be configured is located in the public git repository: https://github.com/terraform-aws-modules/terraform-aws-s3-bucket/tree/master

It is the official AWS module for provisioning an S3 bucket.

Set the ``configuration`` value to the url of the git repository and the ``path`` value to the path where the module files are located. 

The new component definition would look like this:

```
apiVersion: core.oam.dev/v1beta1
kind: ComponentDefinition
metadata:
  annotations:
    definition.oam.dev/description: OFFICIAL Terraform module which creates S3 bucket resources on AWS
  creationTimestamp: null
  labels:
    type: terraform
  name: s3-bucket
  namespace: vela-system
spec:
  schematic:
    terraform:
      configuration: https://github.com/terraform-aws-modules/terraform-aws-s3-bucket.git
      # path:
      providerRef:
        name: aws
        namespace: default
      type: remote
  workload:
    definition:
      apiVersion: terraform.core.oam.dev/v1beta1
      kind: Configuration
status: {}
```

If the module files are located in the root path of the repository it is not necessary to set the ``path`` variable.

To load the definition into your cluster use the following command:

```
kubectl apply -f terraform-aws-s3.yml
```

We check that the new definition has been generated correctly and that KubeVela is able to recognize the necessary variables from the variables.tf file in the git repository.


```
$ vela show s3-bucket
loading terraform module s3-bucket into /home/david/.vela/terraform/s3-bucket from https://github.com/terraform-aws-modules/terraform-aws-s3-bucket.git
Enumerating objects: 1252, done.
Counting objects: 100% (114/114), done.
Compressing objects: 100% (55/55), done.
Total 1252 (delta 68), reused 101 (delta 59), pack-reused 1138

+--------------------------------------------+------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
|                    NAME                    |                                             DESCRIPTION                                              |                           TYPE                            | REQUIRED | DEFAULT |
+--------------------------------------------+------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
| acceleration_status                        | (Optional) Sets the accelerate configuration of an existing bucket. Can be Enabled or Suspended.     | string                                                    | false    |         |
| access_log_delivery_policy_source_accounts | (Optional) List of AWS Account IDs should be allowed to deliver access logs to this bucket.          | list(string)                                              | false    |         |
| access_log_delivery_policy_source_buckets  | (Optional) List of S3 bucket ARNs wich should be allowed to deliver access logs to this bucket.      | list(string)                                              | false    |         |
| acl                                        | (Optional) The canned ACL to apply. Conflicts with `grant`.                                          | string                                                    | false    |         |
| allowed_kms_key_arn                        | The ARN of KMS key which should be allowed in PutObject.                                             | string                                                    | false    |         |
| analytics_configuration                    | Map containing bucket analytics configuration.                                                       | any                                                       | false    |         |
| analytics_self_source_destination          | Whether or not the analytics source bucket is also the destination bucket.                           | bool                                                      | false    |         |
| analytics_source_account_id                | The analytics source account id.                                                                     | string                                                    | false    |         |
| analytics_source_bucket_arn                | The analytics source bucket ARN.                                                                     | string                                                    | false    |         |
| attach_access_log_delivery_policy          | Controls if S3 bucket should have S3 access log delivery policy attached.                            | bool                                                      | false    |         |
| attach_analytics_destination_policy        | Controls if S3 bucket should have bucket analytics destination policy attached.                      | bool                                                      | false    |         |
| attach_deny_incorrect_encryption_headers   | Controls if S3 bucket should deny incorrect encryption headers policy attached.                      | bool                                                      | false    |         |
| attach_deny_incorrect_kms_key_sse          | Controls if S3 bucket policy should deny usage of incorrect KMS key SSE.                             | bool                                                      | false    |         |
| attach_deny_insecure_transport_policy      | Controls if S3 bucket should have deny non-SSL transport policy attached.                            | bool                                                      | false    |         |
| attach_deny_unencrypted_object_uploads     | Controls if S3 bucket should deny unencrypted object uploads policy attached.                        | bool                                                      | false    |         |
| attach_elb_log_delivery_policy             | Controls if S3 bucket should have ELB log delivery policy attached.                                  | bool                                                      | false    |         |
| attach_inventory_destination_policy        | Controls if S3 bucket should have bucket inventory destination policy attached.                      | bool                                                      | false    |         |
| attach_lb_log_delivery_policy              | Controls if S3 bucket should have ALB/NLB log delivery policy attached.                              | bool                                                      | false    |         |
| attach_policy                              | Controls if S3 bucket should have bucket policy attached (set to `true` to use value of `policy` as  | bool                                                      | false    |         |
|                                            | bucket policy).                                                                                      |                                                           |          |         |
| attach_public_policy                       | Controls if a user defined public bucket policy will be attached (set to `false` to allow upstream   | bool                                                      | false    |         |
|                                            | to apply defaults to the bucket).                                                                    |                                                           |          |         |
| attach_require_latest_tls_policy           | Controls if S3 bucket should require the latest version of TLS.                                      | bool                                                      | false    |         |
| block_public_acls                          | Whether Amazon S3 should block public ACLs for this bucket.                                          | bool                                                      | false    |         |
| block_public_policy                        | Whether Amazon S3 should block public bucket policies for this bucket.                               | bool                                                      | false    |         |
| bucket                                     | (Optional, Forces new resource) The name of the bucket. If omitted, Terraform will assign a random,  | string                                                    | false    |         |
|                                            | unique name.                                                                                         |                                                           |          |         |
| bucket_prefix                              | (Optional, Forces new resource) Creates a unique bucket name beginning with the specified prefix.    | string                                                    | false    |         |
|                                            | Conflicts with bucket.                                                                               |                                                           |          |         |
| control_object_ownership                   | Whether to manage S3 Bucket Ownership Controls on this bucket.                                       | bool                                                      | false    |         |
| cors_rule                                  | List of maps containing rules for Cross-Origin Resource Sharing.                                     | any                                                       | false    |         |
| create_bucket                              | Controls if S3 bucket should be created.                                                             | bool                                                      | false    |         |
| expected_bucket_owner                      | The account ID of the expected bucket owner.                                                         | string                                                    | false    |         |
| force_destroy                              | (Optional, Default:false ) A boolean that indicates all objects should be deleted from the bucket so | bool                                                      | false    |         |
|                                            | that the bucket can be destroyed without error. These objects are not recoverable.                   |                                                           |          |         |
| grant                                      | An ACL policy grant. Conflicts with `acl`.                                                           | any                                                       | false    |         |
| ignore_public_acls                         | Whether Amazon S3 should ignore public ACLs for this bucket.                                         | bool                                                      | false    |         |
| intelligent_tiering                        | Map containing intelligent tiering configuration.                                                    | any                                                       | false    |         |
| inventory_configuration                    | Map containing S3 inventory configuration.                                                           | any                                                       | false    |         |
| inventory_self_source_destination          | Whether or not the inventory source bucket is also the destination bucket.                           | bool                                                      | false    |         |
| inventory_source_account_id                | The inventory source account id.                                                                     | string                                                    | false    |         |
| inventory_source_bucket_arn                | The inventory source bucket ARN.                                                                     | string                                                    | false    |         |
| lifecycle_rule                             | List of maps containing configuration of object lifecycle management.                                | any                                                       | false    |         |
| logging                                    | Map containing access bucket logging configuration.                                                  | map(string)                                               | false    |         |
| metric_configuration                       | Map containing bucket metric configuration.                                                          | any                                                       | false    |         |
| object_lock_configuration                  | Map containing S3 object locking configuration.                                                      | any                                                       | false    |         |
| object_lock_enabled                        | Whether S3 bucket should have an Object Lock configuration enabled.                                  | bool                                                      | false    |         |
| object_ownership                           | Object ownership. Valid values: BucketOwnerEnforced, BucketOwnerPreferred or ObjectWriter.           | string                                                    | false    |         |
|                                            | 'BucketOwnerEnforced': ACLs are disabled, and the bucket owner automatically owns and has full       |                                                           |          |         |
|                                            | control over every object in the bucket. 'BucketOwnerPreferred': Objects uploaded to the bucket      |                                                           |          |         |
|                                            | change ownership to the bucket owner if the objects are uploaded with the bucket-owner-full-control  |                                                           |          |         |
|                                            | canned ACL. 'ObjectWriter': The uploading account will own the object if the object is uploaded with |                                                           |          |         |
|                                            | the bucket-owner-full-control canned ACL.                                                            |                                                           |          |         |
| owner                                      | Bucket owner's display name and ID. Conflicts with `acl`.                                            | map(string)                                               | false    |         |
| policy                                     | (Optional) A valid bucket policy JSON document. Note that if the policy document is not specific     | string                                                    | false    |         |
|                                            | enough (but still valid), Terraform may view the policy as constantly changing in a terraform        |                                                           |          |         |
|                                            | plan. In this case, please make sure you use the verbose/specific version of the policy. For more    |                                                           |          |         |
|                                            | information about building AWS IAM policy documents with Terraform, see the AWS IAM Policy Document  |                                                           |          |         |
|                                            | Guide.                                                                                               |                                                           |          |         |
| putin_khuylo                               | Do you agree that Putin doesn't respect Ukrainian sovereignty and territorial integrity? More info:  | bool                                                      | false    |         |
|                                            | https://en.wikipedia.org/wiki/Putin_khuylo!.                                                         |                                                           |          |         |
| replication_configuration                  | Map containing cross-region replication configuration.                                               | any                                                       | false    |         |
| request_payer                              | (Optional) Specifies who should bear the cost of Amazon S3 data transfer. Can be either BucketOwner  | string                                                    | false    |         |
|                                            | or Requester. By default, the owner of the S3 bucket would incur the costs of any data transfer. See |                                                           |          |         |
|                                            | Requester Pays Buckets developer guide for more information.                                         |                                                           |          |         |
| restrict_public_buckets                    | Whether Amazon S3 should restrict public bucket policies for this bucket.                            | bool                                                      | false    |         |
| server_side_encryption_configuration       | Map containing server-side encryption configuration.                                                 | any                                                       | false    |         |
| tags                                       | (Optional) A mapping of tags to assign to the bucket.                                                | map(string)                                               | false    |         |
| versioning                                 | Map containing versioning configuration.                                                             | map(string)                                               | false    |         |
| website                                    | Map containing static web-site hosting or redirect configuration.                                    | any                                                       | false    |         |
| writeConnectionSecretToRef                 | The secret which the cloud resource connection will be written to.                                   | [writeConnectionSecretToRef](#writeConnectionSecretToRef) | false    |         |
+--------------------------------------------+------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+


#### writeConnectionSecretToRef
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
|   NAME    |                                 DESCRIPTION                                  |  TYPE  | REQUIRED | DEFAULT |
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
| name      | The secret name which the cloud resource connection will be written to.      | string | true     |         |
| namespace | The secret namespace which the cloud resource connection will be written to. | string | false    |         |
+-----------+------------------------------------------------------------------------------+--------+----------+---------+

```

Generate a new OAM application to provision with the newly configured module:

```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: provision-s3-official-resource-sample
  namespace: default
spec:
  components:
    - name: activaprefapp-sample-s3-of
      type: s3-bucket
      properties:
        bucket: activaprefapp-bucket-official-module
        writeConnectionSecretToRef:
          name: s3-conn   #required
        providerRef:
          name: aws   #if not specified the default value is 'aws'

```

Now deploy your application so that you can provision the bucket.

```
vela up -f aws-s3-official.yaml
```

Check the status after a few minutes

```
$ vela status provision-s3-official-sample
About:

  Name:         provision-s3-official-sample
  Namespace:    default
  Created at:   2023-09-20 12:28:23 +0200 CEST
  Status:       running

Workflow:

  mode: DAG-DAG
  finished: true
  Suspend: false
  Terminated: false
  Steps
  - id: kqd1bxyuig
    name: activaprefapp-sample-s3-of
    type: apply-component
    phase: succeeded

Services:

  - Name: activaprefapp-sample-s3-of
    Cluster: local  Namespace: default
    Type: s3-bucket
    Healthy Cloud resources are deployed and ready to use
    No trait applied


```

Verify that you have provisioned in the cloud

```
$ aws s3 ls
2023-09-20 12:28:52 activaprefapp-bucket-official-module
```

Check the terraform records that have been generated in the containers within the application POD.

```
$ kubectl -n default get pod
NAME                                     READY   STATUS      RESTARTS   AGE
activaprefapp-sample-s3-of-apply-jr7vj   0/1     Completed   0          4m22s
```

```
$ kubectl -n default logs pod/activaprefapp-sample-s3-of-apply-jr7vj -c terraform-init

Initializing the backend...

Successfully configured the backend "kubernetes"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Finding hashicorp/aws versions matching ">= 4.9.0"...
- Installing hashicorp/aws v5.17.0...
- Installed hashicorp/aws v5.17.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.

```

```
$ kubectl -n default logs pod/activaprefapp-sample-s3-of-apply-jr7vj -c terraform-executor

data.aws_partition.current: Reading...
data.aws_region.current: Reading...
data.aws_partition.current: Read complete after 0s [id=aws]
data.aws_region.current: Read complete after 0s [id=eu-west-1]
data.aws_caller_identity.current: Reading...
data.aws_caller_identity.current: Read complete after 0s [id=454836871817]

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.this[0] will be created
  + resource "aws_s3_bucket" "this" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "activaprefapp-bucket-official-module"
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = false
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)
    }

  # aws_s3_bucket_public_access_block.this[0] will be created
  + resource "aws_s3_bucket_public_access_block" "this" {
      + block_public_acls       = true
      + block_public_policy     = true
      + bucket                  = (known after apply)
      + id                      = (known after apply)
      + ignore_public_acls      = true
      + restrict_public_buckets = true
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + s3_bucket_arn                           = (known after apply)
  + s3_bucket_bucket_domain_name            = (known after apply)
  + s3_bucket_bucket_regional_domain_name   = (known after apply)
  + s3_bucket_hosted_zone_id                = (known after apply)
  + s3_bucket_id                            = (known after apply)
  + s3_bucket_lifecycle_configuration_rules = ""
  + s3_bucket_policy                        = ""
  + s3_bucket_region                        = (known after apply)
  + s3_bucket_website_domain                = ""
  + s3_bucket_website_endpoint              = ""
aws_s3_bucket.this[0]: Creating...
aws_s3_bucket.this[0]: Creation complete after 1s [id=activaprefapp-bucket-official-module]
aws_s3_bucket_public_access_block.this[0]: Creating...
aws_s3_bucket_public_access_block.this[0]: Creation complete after 0s [id=activaprefapp-bucket-official-module]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

s3_bucket_arn = "arn:aws:s3:::activaprefapp-bucket-official-module"
s3_bucket_bucket_domain_name = "activaprefapp-bucket-official-module.s3.amazonaws.com"
s3_bucket_bucket_regional_domain_name = "activaprefapp-bucket-official-module.s3.eu-west-1.amazonaws.com"
s3_bucket_hosted_zone_id = "Z1BKCTXD74EZPE"
s3_bucket_id = "activaprefapp-bucket-official-module"
s3_bucket_lifecycle_configuration_rules = ""
s3_bucket_policy = ""
s3_bucket_region = "eu-west-1"
s3_bucket_website_domain = ""
s3_bucket_website_endpoint = ""

```

*[Back to contents](../README.md)*