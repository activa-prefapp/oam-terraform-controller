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



*[Back to contents](../README.md)*