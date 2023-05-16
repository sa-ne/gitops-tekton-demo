## Quarkus and Tekton pipeline with ACS scanning

This repo provides a gitops based implementation of a demo using a quarkus application that is built, packaged, scanned and pushed (to quay.io) using Tekton pipelines, Red Hat Advanced Cluster Security and Red Hat DevSpaces.


### Provisioning
Clone this or user the raw files url if preferred, then assuming a fresh install of OpenShift run the first command to install the Red Hat GitOps operator:

```
oc create -k gitops/manifests/operators/openshift-gitops-operator/overlays/stable
```

Once the operator is installed, we use the App of Apps pattern to initiate the install of all other operators, including the creation of the pipeline and integration of ACS with the Internal Registry. Notice this might take a while to finish the sync and install everything.

```
oc create -k gitops/manifests/cluster/bootstrap/base
```

### Idea and execution

The workflow starts with DevSpaces where you can execute the demo application that is quarkus based by executing `./mvnw quarkus:dev`. It runs and exposes the applicationfor testing and prototyping. When you are done with coding it's a matter of pushing the code to the [github repository](https://github.com/samueltauil/quarkus-meat-helper) (feel free to fork it and change on your own version) and the pipeline will be triggered.
For this to work you have to configure the webhook in the git repository, you can get the webhook url by executing this:

```
oc get route quarkus-pipeline-event-listener -n demo-quarkus-pipeline -o jsonpath='{.spec.host}'
```
