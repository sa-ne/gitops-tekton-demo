## GitOps based demo of quarkus and tekton pipelines

```
oc create -k gitops/manifests/operators/openshift-gitops-operator/overlays/stable
```

```
oc create -k gitops/manifests/cluster/bootstrap/base
```
