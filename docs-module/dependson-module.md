# Dependent modules configuration

It may be necessary to configure the provisioning of several modules that depend on each other.

For this we can use the dependsOn property of KubeVela to establish an order in the provisioning, ensuring that some services are available for the deployment of the following ones.

In the following example two S3 buckets depend on each other and the RDS module will not provision the services for the database until both buckets are available.

```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: example-dependson-terraform
  namespace: default
spec:
  components:
    - name: activaprefapp-sample1-s3
      type: aws-s3
      properties:
        bucket: activaprefapp-bucket-test-1000
        writeConnectionSecretToRef:
          name: s3-conn1
        providerRef:
          name: aws
    - name: activaprefapp-sample2-s3
      type: aws-s3
      dependsOn:
      - activaprefapp-sample1-s3
      properties:
        bucket: activaprefapp-bucket-test-2000
        writeConnectionSecretToRef:
          name: s3-conn2
        providerRef:
          name: aws
    - name: activaprefapp-sample-db
      type: aws-rds
      dependsOn:
      - activaprefapp-sample1-s3
      - activaprefapp-sample2-s3
      properties:
        identifier: activaprefapp-db-test
        engine: mysql
        family: mysql8.0 
        engine_version: '8.0.33'
        major_engine_version: '8.0'
        instance_class: db.m5d.large
        allocated_storage: 5
        db_name: activaprefappdb
        username: activa
        port: 3306
        iam_database_authentication_enabled: true
        tags: 
          Owner: user
          Environment: dev
        writeConnectionSecretToRef:
          name: db-conn1
        providerRef:
          name: aws
```

You can display the example using the command:

```
vela up -f dependent-module.yaml
```
You can check the organized provisioning of your modules, through the terraform addon, directly using the command: 

```
$ vela status example-dependson-terraform -n default
About:

  Name:         example-dependson-terraform
  Namespace:    default
  Created at:   2023-09-14 19:49:04 +0200 CEST
  Status:       runningWorkflow

Workflow:

  mode: DAG-DAG
  finished: false
  Suspend: false
  Terminated: false
  Steps
  - id: gw3kibiz2v
    name: activaprefapp-sample1-s3
    type: apply-component
    phase: running
    message: wait healthy
  - id: s37bsrrtk7
    name: activaprefapp-sample2-s3
    type: builtin-apply-component
    phase: pending
    message: Pending on DependsOn: activaprefapp-sample1-s3
  - id: eypl19mfe6
    name: activaprefapp-sample-db
    type: builtin-apply-component
    phase: pending
    message: Pending on DependsOn: activaprefapp-sample1-s3

Services:

  - Name: activaprefapp-sample1-s3
    Cluster: local  Namespace: default
    Type: aws-s3
    Unhealthy container "terraform-init" in pod "activaprefapp-sample1-s3-apply-t6k4x" is waiting to start: PodInitializing
    No trait applied
```


```
$ vela status example-dependson-terraform -n default
About:

  Name:         example-dependson-terraform
  Namespace:    default
  Created at:   2023-09-14 19:49:04 +0200 CEST
  Status:       running

Workflow:

  mode: DAG-DAG
  finished: true
  Suspend: false
  Terminated: false
  Steps
  - id: gw3kibiz2v
    name: activaprefapp-sample1-s3
    type: apply-component
    phase: succeeded
  - id: s37bsrrtk7
    name: activaprefapp-sample2-s3
    type: apply-component
    phase: succeeded
  - id: eypl19mfe6
    name: activaprefapp-sample-db
    type: apply-component
    phase: succeeded

Services:

  - Name: activaprefapp-sample-db
    Cluster: local  Namespace: default
    Type: aws-rds
    Healthy Cloud resources are deployed and ready to use
    No trait applied

  - Name: activaprefapp-sample2-s3
    Cluster: local  Namespace: default
    Type: aws-s3
    Healthy Cloud resources are deployed and ready to use
    No trait applied

  - Name: activaprefapp-sample1-s3
    Cluster: local  Namespace: default
    Type: aws-s3
    Healthy Cloud resources are deployed and ready to use
    No trait applied

```

*[Back to contents](../README.md)*