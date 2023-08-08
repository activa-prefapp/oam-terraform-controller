# Custom module configuration

You can add your custom modules by configuring a new CRD for each module.

In this demo a new ComponentDefinition is generated to add the ability to provision a custom module. The module to be configured is located in a public git repository: https://github.com/prefapp/tfm/tree/main/modules/aws-sso

The new component definition would look like this:

```
apiVersion: core.oam.dev/v1beta1
kind: ComponentDefinition
metadata:
  annotations:
    definition.oam.dev/description: Authentication solution that allows users to log in to multiple applications and websites with a single user authentication
  creationTimestamp: null
  labels:
    type: terraform
  name: aws-sso
  namespace: vela-system
spec:
  schematic:
    terraform:
      configuration: https://github.com/prefapp/tfm.git
      path: modules/aws-sso
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

Load your new definition in your cluster.

```
kubectl apply -f path/terraform-aws-sso.yml
```

You can verify that your custom module now appears in the list ready for provisioning by OAM.

```
$ vela components --label type=terraform
NAME                                            DEFINITION                              DESCRIPTION

aws-sso                                         configurations.terraform.core.oam.dev   Authentication solution that allows users to log in to
                                                                                        multiple applications and websites with a single user
                                                                                        authentication
```

You can check the specification of the new cloud resource using the ``vela show <component type name>`` command. For this case: 

```
$ vela show aws-sso

loading terraform module aws-sso into /xxx/yyy/.vela/terraform/aws-sso from https://github.com/prefapp/tfm.git
Enumerating objects: 257, done.
Counting objects: 100% (92/92), done.
Compressing objects: 100% (63/63), done.
Total 257 (delta 70), reused 29 (delta 29), pack-reused 165

+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
|            NAME            |                            DESCRIPTION                             |                           TYPE                            | REQUIRED | DEFAULT |
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+
| data_file                  | absolute path of the data to use (in YAML format).                 | string                                                    | true     |         |
| identity_store_arn         | the arn of the SSO instance.                                       | string                                                    | true     |         |
| store_id                   | the Identity store id.                                             | string                                                    | true     |         |
| writeConnectionSecretToRef | The secret which the cloud resource connection will be written to. | [writeConnectionSecretToRef](#writeConnectionSecretToRef) | false    |         |
+----------------------------+--------------------------------------------------------------------+-----------------------------------------------------------+----------+---------+


#### writeConnectionSecretToRef
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
|   NAME    |                                 DESCRIPTION                                  |  TYPE  | REQUIRED | DEFAULT |
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
| name      | The secret name which the cloud resource connection will be written to.      | string | true     |         |
| namespace | The secret namespace which the cloud resource connection will be written to. | string | false    |         |
+-----------+------------------------------------------------------------------------------+--------+----------+---------+
```

*[Back to contents](../README.md)*