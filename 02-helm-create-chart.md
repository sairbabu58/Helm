# create first Helm chart
```
$ mkdir httpd
$ helm ls
$ helm create firstchart (default 'firstchart' is nginx)
$ ls 
```
```
$ helm install firstapp firstchart (execute this command from current chart directory)
```


# Helm packaging (packaging the application. application.tgz)
```
$ helm package firstchart
$ helm packkage firstchart -d (--destination) /my-path/
```
# Helm lint (code validation)
```
$ helm lint firstchart
```

```
$ helm create my-demo-app
$ tree my-demo-app
$ helm show chart my-demo-app
$ helm show values my-demo-app
$ helm show all my-demo-app
$ helm show --help
```
```
$ helm install demo-app my-demo-app 
$ helm install release-name chart-reference --dry-run  --values values.yaml
$ helm install demo-v1 my-demo-app --dry-run  --values values.yaml
```
```
$ helm list
$ helm history release_name
$ helm rollback release_name revision
```
```
$ helm list 
$ helm remove demo-app 
```


```

$ helm repo add  bitnami https://charts.bitnami.com/bitnami
$ helm search repo --versions
$ helm search repo bitnami --versions
$ helm show chart bitnami/wordpress 
$ helm show values bitnami/wordpress
$ helm show values bitnami/wordpress --version=17.1.8 
```
```
$ helm search repo nginx --versions
$ helm install nginx bitnami/nginx --version=13.2.22
```
```
$ helm  upgrade nginx bitnami/nginx --version=15.5.3
$ helm upgrade  nginx bitnami/nginx -f values.yaml --version=15.5.3
```
```
$  helm history  nginx
REVISION        UPDATED                         STATUS          CHART           APP VERSION     DESCRIPTION     
1               Fri Jan  5 07:16:31 2024        superseded      nginx-13.2.22   1.23.3          Install complete
2               Fri Jan  5 07:24:14 2024        deployed        nginx-15.5.3    1.25.3          Upgrade complete
$ 
```
```
$ helm rollback nginx 1
$ helm list 
```














