## Quarkus and Tekton pipeline with ACS scanning

This repo provides a gitops based implementation of a demo using a quarkus application that is built, packaged, scanned and pushed (to quay.io) using Tekton pipelines, Red Hat Advanced Cluster Security and Red Hat DevSpaces.


### Provisioning
Clone this or user the raw files url if preferred, then assuming a fresh install of OpenShift run the first command to install the Red Hat GitOps operator:

```
oc create -k gitops/manifests/operators/openshift-gitops-operator/overlays/latest
```

Once the operator is installed, we use the App of Apps pattern to initiate the install of all other operators, including the creation of the pipeline and integration of ACS with the Internal Registry. Notice this might take a while to finish the sync and install everything.

```
oc create -k gitops/manifests/cluster/bootstrap/base
```

In order to push the created image during the pipeline to quay.io you have to create a robot account for your repo on quay and use the provided username and password to create a secret and link it to the ServiceAccount.

```
oc create secret docker-registry quay-registry --docker-server=quay.io --docker-username=<robot-username> --docker-password=<password-token> --docker-email=<email> -n demo-quarkus-pipeline
```

Then link it to the proper ServiceAccount created to run the pipeline.

```
oc secrets link pipeline quay-registry --for=pull,mount -n demo-quarkus-pipeline
```
### Idea and execution

The workflow starts with DevSpaces where you can execute the demo application that is quarkus based by executing `./mvnw quarkus:dev`. It runs and exposes the applicationfor testing and prototyping. When you are done with coding it's a matter of pushing the code to the [github repository](https://github.com/samueltauil/quarkus-meat-helper) (feel free to fork it and change on your own version) and the pipeline will be triggered.
For this to work you have to configure the webhook in the git repository, you can get the webhook url by executing this:

```
oc get route quarkus-pipeline-event-listener -n demo-quarkus-pipeline -o jsonpath='{.spec.host}'
```
