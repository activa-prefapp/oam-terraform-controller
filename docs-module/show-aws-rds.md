
| NAME | DESCRIPTION   | TYPE  | REQUIRED | DEFAULT |
| - | - | - | - | -
| allocated_storage                        | The allocated storage in gigabytes.                                                                | string               | false    |         |
| allow_major_version_upgrade              | Indicates that major version upgrades are allowed. Changing this parameter does not result in an | bool                 | false    |         |
|                                         | outage and the change is asynchronously applied as soon as possible.                             |                      |          |         |
| apply_immediately                        | Specifies whether any database modifications are applied immediately, or during the next maintenance | bool                 | false    |         |
|                                         | window.                                                                                            |                      |          |         |
| auto_minor_version_upgrade               | Indicates that minor engine upgrades will be applied automatically to the DB instance during the   | bool                 | false    |         |
|                                         | maintenance window.                                                                                |                      |          |         |
| availability_zone                        | The Availability Zone of the RDS instance.                                                         | string               | false    |         |
| backup_retention_period                  | The days to retain backups for.                                                                    | number               | false    |         |
| backup_window                            | The daily time range (in UTC) during which automated backups are created if they are enabled.      | string               | false    |         |
|                                         | Example: '09:46-10:16'. Must not overlap with maintenance_window.                                  |                      |          |         |
| blue_green_update                        | Enables low-downtime updates using RDS Blue/Green deployments.                                     | map(string)          | false    |         |
| ca_cert_identifier                       | Specifies the identifier of the CA certificate for the DB instance.                                | string               | false    |         |
| character_set_name                       | The character set name to use for DB encoding in Oracle instances. This can't be changed.          | string               | false    |         |
|                                         | See Oracle Character Sets Supported in Amazon RDS and Collations and Character Sets for Microsoft  |                      |          |         |
|                                         | SQL Server for more information. This can only be set on creation.                                 |                      |          |         |
| cloudwatch_log_group_kms_key_id          | The ARN of the KMS Key to use when encrypting log data.                                            | string               | false    |         |
| cloudwatch_log_group_retention_in_days   | The number of days to retain CloudWatch logs for the DB instance.                                  | number               | false    |         |
| copy_tags_to_snapshot                    | On delete, copy all Instance tags to the final snapshot.                                           | bool                 | false    |         |
| create_cloudwatch_log_group              | Determines whether a CloudWatch log group is created for each `enabled_cloudwatch_logs_exports`.   | bool                 | false    |         |
| create_db_instance                       | Whether to create a database instance.                                                             | bool                 | false    |         |
| create_db_option_group                   | Create a database option group.                                                                    | bool                 | false    |         |
| create_db_parameter_group                | Whether to create a database parameter group.                                                      | bool                 | false    |         |
| create_db_subnet_group                   | Whether to create a database subnet group.                                                         | bool                 | false    |         |
| create_monitoring_role                   | Create IAM role with a defined name that permits RDS to send enhanced monitoring metrics to        | bool                 | false    |         |
|                                         | CloudWatch Logs.                                                                                   |                      |          |         |
| custom_iam_instance_profile              | RDS custom IAM instance profile.                                                                   | string               | false    |         |
| db_instance_tags                         | Additional tags for the DB instance.                                                               | map(string)          | false    |         |
| db_name                                  | The DB name to create. If omitted, no database is created initially.                               | string               | false    |         |
| db_option_group_tags                     | Additional tags for the DB option group.                                                           | map(string)          | false    |         |
| db_parameter_group_tags                  | Additional tags for the DB parameter group.                                                        | map(string)          | false    |         |
| db_subnet_group_description              | Description of the DB subnet group to create.                                                      | string               | false    |         |
| db_subnet_group_name                     | Name of DB subnet group. DB instance will be created in the VPC associated with the DB subnet group.| string               | false    |         |
|                                         | If unspecified, will be created in the default VPC.                                                |                      |          |         |
| db_subnet_group_tags                     | Additional tags for the DB subnet group.                                                           | map(string)          | false    |         |
| db_subnet_group_use_name_prefix          | Determines whether to use `subnet_group_name` as is or create a unique name beginning with the     | bool                 | false    |         |
|                                         | `subnet_group_name` as the prefix.                                                                 |                      |          |         |
| delete_automated_backups                 | Specifies whether to remove automated backups immediately after the DB instance is deleted.        | bool                 | false    |         |
| deletion_protection                      | The database can't be deleted when this value is set to true.                                      | bool                 | false    |         |
| domain                                   | The ID of the Directory Service Active Directory domain to create the instance in.                 | string               | false    |         |
| domain_iam_role_name                     | (Required if domain is provided) The name of the IAM role to be used when making API calls to the  | string               | false    |         |
|                                         | Directory Service.                                                                                 |                      |          |         |
| enabled_cloudwatch_logs_exports      | List of log types to enable for exporting to CloudWatch logs. If omitted, no logs will be exported.  | list(string)         | false    |         |
|                                         | Valid values (depending on engine): alert, audit, error, general, listener, slowquery, trace,        |                      |          |         |
|                                         | postgresql (PostgreSQL), upgrade (PostgreSQL).                                                     |                      |          |         |
| engine                                  | The database engine to use.                                                                        | string               | false    |         |
| engine_version                          | The engine version to use.                                                                         | string               | false    |         |
| family                                  | The family of the DB parameter group.                                                              | string               | false    |         |
| final_snapshot_identifier_prefix        | The name which is prefixed to the final snapshot on cluster destroy.                               | string               | false    |         |
| iam_database_authentication_enabled     | Specifies whether or not the mappings of AWS Identity and Access Management (IAM) accounts to    | bool                 | false    |         |
|                                         | database accounts are enabled.                                                                     |                      |          |         |
| identifier                              | The name of the RDS instance.                                                                      | string               | true     |         |
| instance_class                          | The instance type of the RDS instance.                                                             | string               | false    |         |
| instance_use_identifier_prefix          | Determines whether to use `identifier` as is or create a unique identifier beginning with          | bool                 | false    |         |
|                                         | `identifier` as the specified prefix.                                                              |                      |          |         |
| ...                                     |                                                                                                    |                      |          |         |
| timezone                                | Time zone of the DB instance. timezone is currently only supported by Microsoft SQL Server. The    | string               | false    |         |
| iops                                   | The amount of provisioned IOPS. Setting this implies a storage_type of 'io1' or `gp3`. See `notes`   | number              | false    |         |
|                                        | for limitations regarding this variable for `gp3`.                                                   |                     |          |         |
| kms_key_id                             | The ARN for the KMS encryption key. If creating an encrypted replica, set this to the destination    | string                | false    |         |
|                                        | KMS ARN. If storage_encrypted is set to true and kms_key_id is not specified the default KMS key     |                       |          |         |
|                                        | created in your account will be used. Be sure to use the full ARN, not a key alias.                  |                       |          |         |
| license_model                          | License model information for this DB instance. Optional, but required for some DB engines, i.e.     | string                | false    |         |
|                                        | Oracle SE1.                                                                                          |                       |          |         |
| maintenance_window                     | The window to perform maintenance in. Syntax: 'ddd:hh24:mi-ddd:hh24:mi'. Eg: 'Mon:00:00-Mon:03:00'.  | string                | false    |         |
| major_engine_version                   | Specifies the major version of the engine that this option group should be associated with.          | string                | false    |         |
| manage_master_user_password            | Set to true to allow RDS to manage the master user password in Secrets Manager.                      | bool                  | false    |         |
| master_user_secret_kms_key_id          | The key ARN, key ID, alias ARN or alias name for the KMS key to encrypt the master user password     | string                | false    |         |
|                                        | secret in Secrets Manager.\n  If not specified, the default KMS key for your Amazon Web Services     |                       |          |         |
|                                        | account is used.\n.                                                                                  |                       |          |         |
| max_allocated_storage                  | Specifies the value for Storage Autoscaling.                                                         | number                | false    |         |
| monitoring_interval                    | The interval, in seconds, between points when Enhanced Monitoring metrics are collected for the      | number                | false    |         |
|                                        | DB instance. To disable collecting Enhanced Monitoring metrics, specify 0. The default is 0. Valid   |                       |          |         |
|                                        | Values: 0, 1, 5, 10, 15, 30, 60.                                                                     |                       |          |         |
| monitoring_role_arn                    | The ARN for the IAM role that permits RDS to send enhanced monitoring metrics to CloudWatch Logs.    | string                | false    |         |
|                                        | Must be specified if monitoring_interval is non-zero.                                                |                       |          |         |
| monitoring_role_description            | Description of the monitoring IAM role.                                                              | string                | false    |         |
| monitoring_role_name                   | Name of the IAM role which will be created when create_monitoring_role is enabled.                   | string                | false    |         |
| monitoring_role_permissions_boundary   | ARN of the policy that is used to set the permissions boundary for the monitoring IAM role.          | string                | false    |         |
| monitoring_role_use_name_prefix        | Determines whether to use `monitoring_role_name` as is or create a unique identifier beginning with  | bool                  | false    |         |
|                                        | `monitoring_role_name` as the specified prefix.                                                      |                       |          |         |
| multi_az                               | Specifies if the RDS instance is multi-AZ.                                                           | bool                  | false    |         |
| nchar_character_set_name               | The national character set is used in the NCHAR, NVARCHAR2, and NCLOB data types for Oracle          | string                | false    |         |
|                                        | instances. This can't be changed.                                                                    |                       |          |         |
| network_type                           | The type of network stack to use.                                                                    | string                | false    |         |
| option_group_description               | The description of the option group.                                                                 | string                | false    |         |
| option_group_name                      | Name of the option group.                                                                            | string                | false    |         |
| option_group_timeouts                  | Define maximum timeout for deletion of `aws_db_option_group` resource.                               | map(string)           | false    |         |
| option_group_use_name_prefix           | Determines whether to use `option_group_name` as is or create a unique name beginning with the       | bool                  | false    |         |
|                                        | `option_group_name` as the prefix.                                                                   |                       |          |         |
| options                                | A list of Options to apply.                                                                          | any                   | false    |         |
| parameter_group_description            | Description of the DB parameter group to create.                                                     | string                | false    |         |
| parameter_group_name                   | Name of the DB parameter group to associate or create.                                               | string                | false    |         |
| parameter_group_use_name_prefix        | Determines whether to use `parameter_group_name` as is or create a unique name beginning with the    | bool                  | false    |         |
|                                        | `parameter_group_name` as the prefix.                                                                |                       |          |         |
| parameters                             | A list of DB parameters (map) to apply.                                                              | list(map(string))     | false    |         |
| password                               | Password for the master DB user. Note that this may show up in logs, and it will be stored in the    | string                | false    |         |
|                                        | state file.\n  The password provided will not be used if `manage_master_user_password` is set to     |                       |          |         |
|                                        | true.\n.                                                                                             |                       |          |         |
| performance_insights_enabled           | Specifies whether Performance Insights are enabled.                                                  | bool                  | false    |         |
| performance_insights_kms_key_id        | The ARN for the KMS key to encrypt Performance Insights data.                                        | string                | false    |         |
| performance_insights_retention_period  | The amount of time in days to retain Performance Insights data. Valid values are `7`, `731` (2       | number                | false    |         |
|                                        | years) or a multiple of `31`.                                                                        |                       |          |         |
| port                                   | The port on which the DB accepts connections.                                                        | string                | false    |         |
| publicly_accessible                    | Bool to control if instance is publicly accessible.                                                  | bool                  | false    |         |
| putin_khuylo                           | Do you agree that Putin doesn't respect Ukrainian sovereignty and territorial integrity? More info:  | bool                  | false    |         |
|                                        | https://en.wikipedia.org/wiki/Putin_khuylo!.                                                         |                       |          |         |
| replica_mode                           | Specifies whether the replica is in either mounted or open-read-only mode. This attribute is only    | string                | false    |         |
|                                        | supported by Oracle instances. Oracle replicas operate in open-read-only mode unless otherwise       |                       |          |         |
|                                        | specified.                                                                                           |                       |          |         |
| replicate_source_db                    | Specifies that this resource is a Replicate database, and to use this value as the source database.  | string                | false    |         |
|                                        | This correlates to the identifier of another Amazon RDS Database to replicate.                       |                       |          |         |
| restore_to_point_in_time               | Restore to a point in time (MySQL is NOT supported).                                                 | map(string)           | false    |         |
| s3_import                              | Restore from a Percona Xtrabackup in S3 (only MySQL is supported).                                   | map(string)           | false    |         |
| skip_final_snapshot                    | Determines whether a final DB snapshot is created before the DB instance is deleted. If true is      | bool                  | false    |         |
|                                        | specified, no DBSnapshot is created. If false is specified, a DB snapshot is created before the DB   |                       |          |         |
|                                        | instance is deleted.                                                                                 |                       |          |         |
| snapshot_identifier                    | Specifies whether or not to create this database from a snapshot. This correlates to the snapshot ID | string                | false    |         |
|                                        | you'd find in the RDS console, e.g: rds:production-2015-06-26-06-05.                                 |                       |          |         |
| storage_encrypted                      | Specifies whether the DB instance is encrypted.                                                      | bool                  | false    |         |
| storage_throughput                     | Storage throughput value for the DB instance. See `notes` for limitations regarding this variable    | number                | false    |         |
|                                        | for `gp3`.                                                                                           |                       |          |         |
| storage_type                           | One of 'standard' (magnetic), 'gp2' (general purpose SSD), 'gp3' (new generation of general purpose  | string                | false    |         |
|                                        | SSD), or 'io1' (provisioned IOPS SSD). The default is 'io1' if iops is specified, 'gp2' if not. If   |                       |          |         |
|                                        | you specify 'io1' or 'gp3' , you must also include a value for the 'iops' parameter.                 |                       |          |         |
| subnet_ids                             | A list of VPC subnet IDs.                                                                            | list(string)          | false    |         |
| tags                                   | A mapping of tags to assign to all resources.                                                        | map(string)           | false    |         |
| timeouts                               | Updated Terraform resource management timeouts. Applies to `aws_db_instance` in particular to permit | map(string)           | false    |         |
|                                        | resource management times.                                                                           |
|                                         | timezone can only be set on creation. See MSSQL User Guide for more information.                   |                      |          |         |
| username                                | Username for the master DB user.                                                                   | string               | false    |         |
| vpc_security_group_ids                  | List of VPC security groups to associate.                                                          | list(string)         | false    |         |
| writeConnectionSecretToRef              | The secret which the cloud resource connection will be written to.                                 | [writeConnectionSecretToRef](#writeConnectionSecretToRef) | false    |         |

#### writeConnectionSecretToRef
|   NAME    |                                 DESCRIPTION                                  |  TYPE  | REQUIRED | DEFAULT |
|-----------|------------------------------------------------------------------------------|--------|----------|---------|
| name      | The secret name which the cloud resource connection will be written to.      | string | true     |         |
| namespace | The secret namespace which the cloud resource connection will be written to. | string | false    |         |


*[Back to Provision Cloud - AWS RDS](aws-rds.md)*