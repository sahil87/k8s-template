# k8s-template

Template for Kubernetes GitOps config

The below assumes that the name of your project (a sizeable collection of apps) is `teaxr`, and within the `teaxr` project, you have an app (a sizable collection of repos/docker images) named `teaxr-1api`. The repos/docker images within teaxr-1api in this example are `teaxrapi`, `z5api`, and `swagger`.

> Note: the groups above are pure convenience (not a convention), and depend on your use case.

## Folder Descriptions

* base - this is the folder containing most of the kubernetes object definitions. Its referred to by the other namespace folders. ImageTags get defined here.
* dev/stage/prod - dev, stage and prod are the namespace folders containing everything that different from base folder for these namespaces. This is the folder containing the apps given to argocd.
* utils - contains things necessary before the main app can be installed - like any private docker keys. This is installed in the same namespace as the app.

## Folder structure reference

* base/ - k8s base config.
  * projects/ (teaxr) -> name of the argocd "project" object
    * apps/ (teaxr-1api) -> Folder given to argocd for creating argocd apps
      * kustomization.yaml
      * repo1
      * repo2
      * repo3
        * kustomization.yaml
        * resources
          * deployment.yaml
          * service.yaml
          * ingress.yaml
* namespace/ (dev/stage/prod)
  * setup/ -> whatever you need to setup argocd, repos' ssh access, docker secret keys, etc.
    * 
  * project/
    * apps/
      * kustomization.yaml
      * envs
        * repo1.env
        * repo2.env
        * repo3.env
      * overlays
        * ingress-repo1.yaml
        * ingress-repo2.yaml
        * ingress-repo3.yaml

### ArgoCd command to deploy the apps folder

```sh
PREFIX=dev; STR_NM="--dest-namespace dev --repo git@github.com:sahil87/k8s-template"
STR_PROJ="--project utils --sync-policy automated --auto-prune"
STR_DEF="--revision HEAD --dest-server https://kubernetes.default.svc"

argocd app create $PREFIX-utils-1keys --path dev/utils/utils-1keys $(echo $STR_PROJ $STR_NM $STR_DEF)

PREFIX=dev; STR_NM="--dest-namespace dev --repo git@github.com:sahil87/k8s-template"
STR_PROJ="--project teaxr --sync-policy automated --auto-prune"
STR_DEF="--revision HEAD --dest-server https://kubernetes.default.svc"

argocd app create $PREFIX-teaxr-1api --path dev/teaxr/teaxr-1api $(echo $STR_PROJ $STR_NM $STR_DEF)
```
