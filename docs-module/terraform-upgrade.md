# Terraform upgrade

In some cases you may need to use a higher version of Terraform than the default version installed with the addon installation. 

Before deploying the updated driver we need to disable the addon if we have previously installed it by the usual means.

```
vela addon disable terraform
```
__ATTENTION__: before making changes check that you do not have any provisioning process in progress.

## Docker terraform upgrade

For this we will first need to generate a docker-terraform image with the version we need.

To simplify the process we have made a fork in this organization of the original repository used by the addon. It has been modified and generated a new image updated to Terraform 1.5.5 version.

- The original repository of the image used by the addon: https://github.com/oam-dev/docker-terraform

- The new repository: https://github.com/activa-prefapp/docker-terraform

- The new container image: ghcr.io/activa-prefapp/docker-terraform:1.5.5


## Standalone Terraform Controller

According to the official documentation we can deploy the Terraform controller [using helm](https://github.com/kubevela/terraform-controller/blob/master/getting-started.md#standalone-terraform-controller). 

Before performing the deployment you must make the necessary modifications in the [values.yaml](../terraform-controller/values.yaml) file and configure the ``terraformImage`` variable with the new generated container image.

Add helm repository:

```
helm repo add kubevela-addons https://charts.kubevela.net/addons
```

To deploy use the command:

```
helm upgrade --install terraform-controller -f values.yaml kubevela-addons/terraform-controller
```

Once the chart deployment is finished you have your new updated controller configured.

## Standalone Terraform Controller with OAM

For this demostration we have defined an OAM application to deploy the helm chart with the new changes in [/terraform-controller](../terraform-controller/).

```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: terraform-controller
  namespace: vela-system
spec:
  components:
    - name: terraform-controller
      type: helm
      properties:
        repoType: "helm"
        url: "https://charts.kubevela.net/addons/"
        valuesFiles:
          - "values.yaml"
```

To deploy the controller use the command:

```
vela up -f terraform-controller.yaml
```



*[Back to contents](../README.md)*