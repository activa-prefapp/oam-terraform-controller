# Provision Cloud - AWS RDS

All supported Terraform cloud resources can be seen in the [list](https://kubevela.io/docs/end-user/components/cloud-services/cloud-resources-list/). You can also filter them by command ``vela components --label type=terraform``

You can check the specification of a cloud resource using the ``vela show <component type name> command``.

In this case we run the command ``vela show aws-rds``.

```
+----------------------------------------+------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
|                  NAME                  |                                             DESCRIPTION                                              |                           TYPE                            | REQUIRED | DEFAULT |
+----------------------------------------+------------------------------------------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
| allocated_storage                      | The allocated storage in gigabytes.                                                                  | string                                                    | false    |         |
| allow_major_version_upgrade            | Indicates that major version upgrades are allowed. Changing this parameter does not result in an     | bool                                                      | false    |         |
|                                        | outage and the change is asynchronously applied as soon as possible.                                 |                                                           |          |         |
| apply_immediately                      | Specifies whether any database modifications are applied immediately, or during the next maintenance | bool                                                      | false    |         |

..........
```

As the result is too long you can see it [here](./show-aws-rds.md).

With all the above data and following the [initial documentation](../README.md) of this repository or the [original KubeVela documentation](https://kubevela.io/docs/tutorials/consume-cloud-services/) you can generate some OAM applications to provision resources in AWS.

The following OAM application will provision a database on AWS using the RDS module.


```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: provision-rds3-resource-sample
  namespace: default
spec:
  components:
    - name: activaprefapp-sample-db
      type: aws-rds
      properties:
        identifier: activaprefapp-db-test
        engine: mysql   #required
        family: mysql8.0    #required
        engine_version: '8.0.33'
        major_engine_version: '8.0'   #required
        instance_class: db.m5d.large    #required
        allocated_storage: 5    #required
        db_name: activaprefappdb
        username: activa
        port: 3306
        iam_database_authentication_enabled: true
        tags: 
          Owner: user
          Environment: dev
        writeConnectionSecretToRef:
          name: db-conn   #required
        providerRef:
          name: aws   #if not specified the default value is 'aws'
```
Check the status of the application:

```
$ vela ls

APP                             COMPONENT               TYPE        TRAITS  PHASE   HEALTHY STATUS                                              CREATED-TIME
provision-rds3-resource-sample  activaprefapp-sample-db aws-rds             running healthy Cloud resources are deployed and ready to use       2023-08-09 11:20:46 +0200 CEST
```

If you want to visualize the logs look for the pod displayed by your OAM application and apply the command "kubectl logs".


```
Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.db_instance.aws_db_instance.this[0] will be created
  + resource "aws_db_instance" "this" {
      + address                               = (known after apply)
      + allocated_storage                     = 5
      + allow_major_version_upgrade           = false
      + apply_immediately                     = false
      + arn                                   = (known after apply)
      + auto_minor_version_upgrade            = true
      + availability_zone                     = (known after apply)
      + backup_retention_period               = (known after apply)
      + backup_target                         = (known after apply)
      + backup_window                         = (known after apply)
      + ca_cert_identifier                    = (known after apply)
      + character_set_name                    = (known after apply)
      + copy_tags_to_snapshot                 = false
      + db_name                               = "activaprefappdb"
      + db_subnet_group_name                  = (known after apply)
      + delete_automated_backups              = true
      + deletion_protection                   = false
      + endpoint                              = (known after apply)
      + engine                                = "mysql"
      + engine_version                        = "8.0.33"
      + engine_version_actual                 = (known after apply)
      + final_snapshot_identifier             = "final-activaprefapp-db-test-9ab38774"
      + hosted_zone_id                        = (known after apply)
      + iam_database_authentication_enabled   = true
      + id                                    = (known after apply)
      + identifier                            = "activaprefapp-db-test"
      + identifier_prefix                     = (known after apply)
      + instance_class                        = "db.m5d.large"
      + iops                                  = (known after apply)
      + kms_key_id                            = (known after apply)
      + latest_restorable_time                = (known after apply)
      + license_model                         = (known after apply)
      + listener_endpoint                     = (known after apply)
      + maintenance_window                    = (known after apply)
      + manage_master_user_password           = true
      + master_user_secret                    = (known after apply)
      + master_user_secret_kms_key_id         = (known after apply)
      + max_allocated_storage                 = 0
      + monitoring_interval                   = 0
      + monitoring_role_arn                   = (known after apply)
      + multi_az                              = false
      + nchar_character_set_name              = (known after apply)
      + network_type                          = (known after apply)
      + option_group_name                     = "activaprefapp-db-test-20230809093037577200000001"
      + parameter_group_name                  = "activaprefapp-db-test-20230809092721212900000002"
      + performance_insights_enabled          = false
      + performance_insights_kms_key_id       = (known after apply)
      + performance_insights_retention_period = (known after apply)
      + port                                  = 3306
      + publicly_accessible                   = false
      + replica_mode                          = (known after apply)
      + replicas                              = (known after apply)
      + resource_id                           = (known after apply)
      + skip_final_snapshot                   = false
      + snapshot_identifier                   = (known after apply)
      + status                                = (known after apply)
      + storage_encrypted                     = true
      + storage_throughput                    = (known after apply)
      + storage_type                          = (known after apply)
      + tags                                  = {
          + "Environment" = "dev"
          + "Owner"       = "user"
        }
      + tags_all                              = {
          + "Environment" = "dev"
          + "Owner"       = "user"
        }
      + timezone                              = (known after apply)
      + username                              = "activa"
      + vpc_security_group_ids                = (known after apply)

      + timeouts {}
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + db_instance_address                = (known after apply)
  + db_instance_arn                    = (known after apply)
  + db_instance_availability_zone      = (known after apply)
  + db_instance_ca_cert_identifier     = (known after apply)
  + db_instance_endpoint               = (known after apply)
  + db_instance_engine_version_actual  = (known after apply)
  + db_instance_hosted_zone_id         = (known after apply)
  + db_instance_master_user_secret_arn = (known after apply)
  ~ db_instance_name                   = "activaprefapp-db" -> "activaprefappdb"
  + db_instance_resource_id            = (known after apply)
  + db_instance_status                 = (known after apply)
  + db_listener_endpoint               = (known after apply)
module.db_instance.aws_db_instance.this[0]: Creating...
module.db_instance.aws_db_instance.this[0]: Still creating... [10s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [20s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [30s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [40s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [50s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [1m0s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [1m10s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [1m20s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [1m30s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [1m40s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [1m50s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [2m0s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [2m10s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [2m20s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [2m30s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [2m40s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [2m50s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [3m0s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [3m10s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [3m20s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [3m30s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [3m40s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [3m50s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [4m0s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [4m10s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [4m20s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [4m30s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [4m40s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [4m50s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [5m0s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [5m10s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [5m20s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [5m30s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [5m40s elapsed]
module.db_instance.aws_db_instance.this[0]: Still creating... [5m50s elapsed]
module.db_instance.aws_db_instance.this[0]: Creation complete after 5m57s [id=db-ZS6Y4DWMTVEZ4VCO3IC63RPXNE]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

```

The outputs are stored in the secret specified in writeConnectionSecretToRef.name in the OAM application definition.

```
$ kubectl -n default get secrets
NAME                               TYPE                             DATA   AGE
db-conn                            Opaque                           21     14m

```

```
$ kubectl -n default describe secret/db-conn
Name:         db-conn
Namespace:    default
Labels:       terraform.core.oam.dev/created-by=terraform-controller
              terraform.core.oam.dev/owned-by=activaprefapp-sample-db
              terraform.core.oam.dev/owned-namespace=default
Annotations:  <none>

Type:  Opaque

Data
====
db_option_group_id:                  48 bytes
db_parameter_group_arn:              86 bytes
db_instance_resource_id:             29 bytes
db_listener_endpoint:                2 bytes
db_option_group_arn:                 86 bytes
db_instance_engine:                  5 bytes
db_instance_port:                    4 bytes
db_instance_status:                  9 bytes
db_instance_username:                6 bytes
db_parameter_group_id:               48 bytes
db_instance_ca_cert_identifier:      11 bytes
db_instance_endpoint:                67 bytes
db_instance_engine_version_actual:   6 bytes
db_instance_cloudwatch_log_groups:   2 bytes
db_instance_hosted_zone_id:          14 bytes
db_instance_identifier:              21 bytes
db_instance_master_user_secret_arn:  103 bytes
db_instance_name:                    15 bytes
db_instance_address:                 62 bytes
db_instance_arn:                     59 bytes
db_instance_availability_zone:       10 bytes
```



*[Back to contents](../README.md)*