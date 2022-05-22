# Hands-On ArgoCD Workshop

## Cluster Prerquisites

Install the following Operator to your cluter to get started:

- CodeReady Workspaces (for participant environments)
- OpenShift GitOps / ArgoCD (to have participants provision their own ArgoCD Instances)

Create a group for all participants and allow them to create ArgoCD Instances (You can find a Kustomize file at [cluster-preoeration/rolebindings](./cluster-preperation/rolebindings)).

## Workshop Agenda

- Provision your own Workspace
- Deploy an ArgoCD Instance
- Deploy an App via Helm

# Provision your own Workspace

- Fork this repository
- Change the repo in the devfile

- Login to CodeReady Workspaces
- Copy the devfile to your CodeReady Workspace instance
- Login to the console
- create a namespace for your `ArgoCD` Instance
  - `oc new-project userX-argo`

## Deploying your ArgoCD Instance

- Change the name of your ArgoCD Instance
- Create your ArgoCD Instance
  - `oc apply -f argocd.yaml -n userX-argo`

## Deploy an App via Helm

- Create a namespace for this App
  - `oc new-project userX-sharry`
- Tell the ArgoCD Operator, that this namespace is to be managed by your ArgoCD Instance (this is usually done by an administrator)
  - `oc label namespace userX-sharry argocd.argoproj.io/managed-by=userX-argo`

go to [manifests/apps/sharry](./manifests/apps/sharry/)

take a look at [sharry-app.yaml](./manifests/apps/sharry/base/sharry-app.yaml).
Change the `.spec.destination.namespace` to your users namespace e.g. `userX-sharry`

take alook at [kustomization.yaml](./manifests/apps/sharry/base/kustomization.yaml).
Change the namespace to your Argo Namespace `user1-argo`

The initial App will deploy multiple other apps (one for PostgreSQL and one for Sharry). This is called the `app-of-apps` pattern.

Take a look at [sharry-chart.yaml](./manifests/apps/sharry/base/apps/sharry-chart.yaml) and [postgresql-chart.yaml](./manifests/apps/sharry/base/apps/postgresql-chart.yaml).

Change the namespace for these both and the destination to your users namespaces
`metadata.namespace: userX-argo`
`spec.destination.namespace: userX-sharry`

run `kustomize build ./manifests/apps/sharry/base` and verify, that your initial app has the correct namespaces
run `kustomize build ./manifests/apps/sharry/base/apps` and verify that the other apps have the correct namespace as well

Apply the first App to the cluster

`oc apply -k ./manifests/apps/sharry/base`