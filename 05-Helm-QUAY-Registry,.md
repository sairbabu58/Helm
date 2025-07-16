# Note: Quay is not  chartmuseum specific registry. Read the below docs.

https://container-registry.com/docs/getting-started-guide/working-with-helm-charts-and-oci-registries/



```
$ helm create demo-app
$ helm package demo-app
$ helm registry login -u admin 
$ helm push demo-app-0.1.1.tgz oci://quay.example.com:8443/admin/helm (Before push, please create a repo on Quay registry)
$ helm search all oci://quay.example.com:8443/admin/helm/demo-ap
```
```
$ helm pull oci://<QUAY_HOSTNAME>/helm/demo-app --version=0.1.0
$ helm install demo-app-0.1.0.tgz
```
```
$ helm install oci://<QUAY_HOSTNAME>/helm/demo-app --version=0.1.0
```







