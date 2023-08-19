# Automatic provisioning in cloud with OAM GitOps

You can automatically provision the configured modules, and maintain consolidation between the files in your repository and your cloud infrastructure, by defining a GitOps application for KubeVela.

## Additional infrastructure requirements

- FluxCD

## OAM GitOps application deployment 

The following OAM application provisions all the modules of this repository located in the specified path and maintains the consolidation, with the files defined in the path.

```
apiVersion: core.oam.dev/v1beta1
kind: Application
metadata:
  name: automatic-provisioning
  namespace: gitops
spec:
  components:
  - name: aws-automatic-provisioning
    type: kustomize
    properties:
      repoType: git
      # replace it with your repo url
      url: https://github.com/activa-prefapp/oam-terraform-controller
      # replace it with your git secret if it's a private repo
      secretRef: git-auth-secret
      # the pull interval time, set to 10m since the infrastructure is steady
      pullInterval: 1m
      git:
        # the branch name
        branch: terraform-controller-tes
      # the path to sync
      path: ./oam-applications
```

Start the application defined with the command:

```
vela up -f terraform-gitops.yaml
```
Once the above command is launched, all the resources defined in OAM will start to be provisioned in the cloud through the Terraform controller.

It will be enough to remove the deployment from the GitOps application to remove both the OAM components in your cluster and the resources provisioned in the cloud.

```
vela -n gitops delete automatic-provisioning
```

*[Back to contents](../README.md)*