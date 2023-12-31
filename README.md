# OAM Terraform controller

OAM's terraform controller demonstration repository for aws-provider

## Infrastructure requirements

- Kubernetes cluster
- KubeVela


## Enable Terraform addon

To provision cloud resources using terraform in KubeVela you need to enable the corresponding addon.

```
vela addon enable terraform
```

Once the previous step has been completed, you must enable the corresponding Terraform Provider addon. You can see the list of compatible providers ready to be configured.

```
$ vela addon list | grep terraform-
terraform-alibaba       KubeVela    Kubernetes Terraform Controller for Alibaba Cloud                      [1.0.2, 1.0.1]     enabled (1.0.2)
terraform-tencent       KubeVela    Kubernetes Terraform Controller Provider for Tencent Cloud             [1.0.0, 1.0.1]     enabled (1.0.0)
terraform-aws           KubeVela    Kubernetes Terraform Controller for AWS                                [1.0.0, 1.0.1]     enabled (1.0.0)
terraform-azure         KubeVela    Kubernetes Terraform Controller for Azure                              [1.0.0, 1.0.1]     enabled (1.0.0)
terraform-baidu         KubeVela    Kubernetes Terraform Controller Provider for Baidu Cloud               [1.0.0, 1.0.1]     enabled (1.0.0)
terraform-gcp           KubeVela    Kubernetes Terraform Controller Provider for Google Cloud Platform     [1.0.0, 1.0.1]     enabled (1.0.0)
terraform-ucloud        KubeVela    Kubernetes Terraform Controller Provider for UCloud                    [1.0.1, 1.0.0]     enabled (1.0.1)
```

In this particular case, the driver will be enabled for AWS:

```
vela addon enable terraform-aws   
# vela addon enable terraform-<provider-name>
```
You can also disable, upgrade, and check the status of an addon by command ``vela addon``

Now, you can check the supported Terraform providers that are deployed in the cluster.

```
vela config-template list | grep terraform
```

Before you start provisioning or consuming resources in the cloud you must authenticate and configure the provider for your AWS cloud.

```
vela config create aws -t terraform-aws name=aws AWS_ACCESS_KEY_ID=xxx AWS_SECRET_ACCESS_KEY=yyy AWS_DEFAULT_REGION=us-east-1
``` 

You can check the Terraform driver logs and that it is ready to provision resources with the following command

```
kubectl -n vela-system logs deployments/terraform-controller
```

You can configure and authenticate multiple providers. Note that you must later reference the ``name`` of the provider specified in the authentication command above in your provisioning OAM definition. Subsequent OAM definitions in which you do not configure the providerRef will have a default providerRef.name=aws. 

```
$ vela provider ls
TYPE            PROVIDER        NAME    REGION          CREATED-TIME
terraform-aws   aws             aws     eu-west-1       2023-08-07 16:29:36 +0200 CEST
terraform-aws   aws             aws2    eu-west-1       2023-08-08 10:47:10 +0200 CEST
terraform-aws   aws             aws3    eu-north-1      2023-08-10 13:20:03 +0200 CEST
```

## Provision Cloud Resources

All supported Terraform cloud resources can be seen in the [list](https://kubevela.io/docs/end-user/components/cloud-services/cloud-resources-list/). You can also filter them by command ``vela components --label type=terraform``

You can check the specification of a cloud resource using the ``vela show <component type name>`` command.

For example the command ``vela show aws-s3`` will return:

````
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
|            NAME            |                            DESCRIPTION                             |                           TYPE                            | REQUIRED | DEFAULT |
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
| acl                        | S3 bucket ACL.                                                     | string                                                    | false    |         |
| bucket                     | S3 bucket name.                                                    | string                                                    | true     |         |
| writeConnectionSecretToRef | The secret which the cloud resource connection will be written to. | [writeConnectionSecretToRef](#writeConnectionSecretToRef) | false    |         |
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+


#### writeConnectionSecretToRef
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
|   NAME    |                                 DESCRIPTION                                  |  TYPE  | REQUIRED | DEFAULT |
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
| name      | The secret name which the cloud resource connection will be written to.      | string | true     |         |
| namespace | The secret namespace which the cloud resource connection will be written to. | string | false    |         |
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
````


For different vendors, these parameters update accordingly. All cloud resources have the following common parameters.

- ``writeConnectionSecretToRef``: Type, represents the outputs of Terraform will become key/values in the secret with the name specified here ``struct``
    - ``name``, specifies the name of the secret.
    - ``namespace``, specifies the namespace of the secret.
- ``providerRef``: Type, represents the Provider which is referenced by a cloud service ``struct``
    - ``name``, specifies the name of the provider.
- ``deleteResource``: Type, specify whether to delete the corresponding cloud service when the app is deleted. By Default it's ``bool`` ``true``
- ``customRegion``: Type, specify region for resources, it will override the default region from ``string`` ``providerRef``

For example, use the following Application to provision an s3-bucket:

```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: provision-s3-resource-sample
  namespace: default
spec:
  components:
    - name: activaprefapp-sample-s3
      type: aws-s3
      properties:
        bucket: activaprefapp-bucket-test-48754   #required
        writeConnectionSecretToRef:
          name: s3-conn   #required
        providerRef:
          name: aws2   #if not specified the default value is 'aws'
```

The above component will create an s3-bucket with connection information stored in a secreted.

Note that we have specified the value of ``name`` in providerRef because we do not want to use the default value ``aws`` and we have several authenticated providers in our system.

Apply the above application, then check the status:

```
$ vela ls

APP                             COMPONENT                      TYPE    TRAITS  PHASE   HEALTHSTATUS                                         CREATED-TIME
provision-s3-resource-sample    activaprefapp-sample-s3        aws-s3          running healthCloud resources are deployed and ready to use  2023-08-08 11:26:56 +0200 CEST
```

After the phase becomes ``running`` and ``healthy``, you can then check the S3 bucket in AWS console.

```
$ aws s3 ls
2023-09-20 11:37:50 activaprefapp-bucket-test-48754
```

You can check the terraform logs by checking the terraform-init and terraform-executor containers.

First check the pod that has been generated to provision the bucket s3:

```
$ kubectl -n default get pod
NAME                                  READY   STATUS      RESTARTS   AGE
activaprefapp-sample-s3-apply-4b2kn   0/1     Completed   0          7m58s
```

Now consult the container records.

```
$ kubectl -n default logs pod/activaprefapp-sample-s3-apply-4b2kn  -c terraform-init

Initializing the backend...

Successfully configured the backend "kubernetes"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Finding hashicorp/aws versions matching "3.74.3"...
- Installing hashicorp/aws v3.74.3...
- Installed hashicorp/aws v3.74.3 (signed by HashiCorp)

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
$ kubectl -n default logs pod/activaprefapp-sample-s3-apply-4b2kn  -c terraform-executor

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_s3_bucket.bucket-acl will be created
  + resource "aws_s3_bucket" "bucket-acl" {
      + acceleration_status         = (known after apply)
      + acl                         = "private"
      + arn                         = (known after apply)
      + bucket                      = "activaprefapp-bucket-test-48754"
      + bucket_domain_name          = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + BUCKET_NAME = (known after apply)
aws_s3_bucket.bucket-acl: Creating...
aws_s3_bucket.bucket-acl: Creation complete after 1s [id=activaprefapp-bucket-test-48754]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

BUCKET_NAME = "activaprefapp-bucket-test-48754.s3.amazonaws.com"

```


## Terraform version upgrade

The default version of Terraform, set by the addon, may not be in the version required for some of your cloud provisioning.
To support the newly developed modules you should follow the steps defined in the [terraform-upgrade](./docs-module/terraform-upgrade.md) section.

## Custom module integration

For the integration of a new custom module for AWS cloud provisioning you will need to generate its respective ComponentDefinition.

In the [modules-definition](./modules-definition/) path you can see some examples of ComponentDefinition for integrating custom modules in public GitHub repositories.

You can follow step by step the configuration of a custom module in the [custom-modules](./docs-module/custom-modules.md) section.

## Automatic provisioning with OAM GitOps

You can automatically provision the configured modules, and maintain consolidation between the files in your repository and your cloud infrastructure, by defining a GitOps application for KubeVela that points to the path of the defined OAM provisioning applications.

You can see an example of this [here](./docs-module/terraform-gitops.md).

## Organized provisioning with dependsOn

You can set the provisioning order of your services by setting the dependsOn value in your terraform OAM applications. You can see an example in the [dependsOn section](./docs-module/dependson-module.md) of the documentation.


## List OAM applications defined

These are the OAM applications defined in this repository for the demonstration of KubeVela's Terraform addon to AWS:

- OAM application for S3 provisioning
- OAM application for RDS provisioning
- OAM application for pvc provisioning

Some of these applications have been supplemented with more documentation to extend the demonstration level.

- [AWS-RDS module docs](./docs-module/aws-rds.md)

It has been necessary to generate a new ComponentDefinition to add a new module for provisioning with the Terraform addon for KubeVela.

- [Custom module configuration for AWS-SSO](./docs-module/aws-sso-module.md)

The indicated [aws-sso custom module](https://github.com/prefapp/tfm/tree/main/modules/aws-sso) cannot be integrated. Added recommendations for the developed aws-sso provisioning module.

 - [AWS-SSO custom module recommendations](./docs-module/aws-sso-recommendations.md)