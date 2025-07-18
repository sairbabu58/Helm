# Helm repo add and remove
```
$ helm repo list
$ helm repo add bitnami https://charts.bitnami.com/bitnami
$ helm repo remove bitnami
```


# Helm search application
```
$ helm search mysql
$ helm search nginx
$ helm search nginx --version
$ helm search repo do280-helm --versions
```


# Helm install application
```
$ helm install mydb bitnami/mysql
(note: it will show the details, how to access mysql server and other details)
$ heml status mydb 
(get the above note details)
$ helm install mydb bitnami/mysql -n namespace
```
```
$ helm install mydb bitnami/mysql --dry-run
$ helm install mydb bitnami/mysql --values /my-path/values.yaml --dry-run

```

# Helm list and uninstall applications
```
$ helm list 
$ helm list -n namespace
$ helm uninstall mydb
$ helm uninstall mydb -n namespace
```


# Helm install with custom values
```
$ helm install mydb bitnami/mysql --values /my-path/values.yaml

before, create values.yaml (ref the details: https://github.com/bitnami/charts/blob/main/bitnami/mysql/values.yaml)
$ vi values.yaml
  auth:
    rootPassword: "redhat"
 ```   
    
    
# Helm repo update
```
$ helm repo update 
```


# Helm application update
```
$ helm list
$ helm list -n namespace
$ helm status mydb (get the update command for update application)
$ helm upgrade mydb bitnami/mysql --values values.yaml (for upgrade database application)
$ helm upgrade myapp bitnami/myapp
```
```
$ helm upgrade mydb bitnami/mysql --values values.yaml --dry-run
$ helm upgrade mydb bitnami/mysql --dry-run
$ helm upgrade mydb bitnami/mysql --force (it will delete the all delpoyment and pods and get the completely latest files and new pods)
```

# Validation :
```
$ helm list 
$ helm status myapp
``` 
(it will show the revision:2 \\ earlier the was revision: 1, now the is revision: 2 \\ using this revision we can rollout the application \\ revision data is available in side of secret on the same namespace)



# Helm template 
# get application code before deploy the application
# get the yaml template and re-use it on k8s and openshift ENV for create your own application
```
$ helm repo list
$ helm template j2c-repo/mysql
$ helm template bitnami/mysql --values /my-path/values.yaml 
```


# How to get all the helm application deployment code.
# Get application code after deployed your application. how this app got deployed with all the resources
```
$ helm list -A
$ helm get manifest nfs-storage -n nfs-storage
```

    
    
# Helm get commands
```
$ helm list
$ helm get notes mydb
$ helm get values mydb (it will show the custom values you have provided during the installation. like auth.rootPassword, image.pullPolicy ..etc)
$ helm get values mydb --all (it will show the all default values as well as custom values )
$ helm get values mydb --revision 1 (revision 1 values)
$ helm get values mydb --revision 2 (revision 2 new changes values)
$ helm get --help
```


# Helm history commands
```
$ helm history mydb (get the application details like: revision details, installation date, description: instaled/upgrade, if application failed due to any reason, )
```


# Helm rollout
```
$ helm ls
$ helm list
$ helm history mydb

$ helm history mydb (get the version name 1 or 2 or 3)
$ helm rollback mydb 2
$ helm rollback mydb 1

$ helm history mydb
$ kubectl get secret
```

# Helm restore application after uninstalled
```
$ helm uninstall mydb --keep-history
$ helm ls (deleted the application)
$ helm history mydb (still you can see the details)
$ helm rollback mydb 3
$ helm ls (see the application)
```

# Helm create namespace and install application
```
$ helm install mydb bitnami/mysql --namespace demo --create-namespace 
```


# Helm upgrade or install
```
$ helm upgrade --install mydb bitnami/mysql 
(First it will check if mysql installed or not, if already insttalled it will do the latest upgrade else do the fresh install)
```


# Helm --wait and --timeout
```
$ helm install myapache bitnami/apache --wait --timeout 10m20s (it is important if your ENV is taking much time to pull the image to create application)
$ helm install myapache bitnami/apache --automic (during time period if its got failed, it will rollback the version)
```



# How to check application available version
```
$ helm repo add test <reponame>
$ helm repo list
$ helm search repo test --version 1.23.0 (available version)
$ helm search repo test --version 1.23.1 (Not available version)
```



```

$ helm repo list
$ helm repo add do280-repo  http://helm.ocp4.example.com/charts
$ helm repo list 
$ helm search repo 
$ helm search repo --versions
```
```
$ helm search hub
```
```
$ helm show values do280-repo/etherpad 
$ helm show values do280-repo/etherpad --version 0.0.6
```
```
$ vi values.yaml (Over write the default values.yaml file)
image:
  repository: registry.ocp4.example.com:8443/etherpad
  name: etherpad
  tag: 1.8.18
route:
  host: development-etherpad.apps.ocp4.example.com
```
```
$ helm install example-app do280-repo/etherpad  -f values.yaml --version 0.0.6
$ oc get po
$ oc get route 
```
```
$ helm list
$ helm search repo --versions
```
```
$ helm upgrade example-app do280-repo/etherpad  -f values.yaml --version 0.0.7 (Upgrade the Application Version from 0.0.6 to 0.0.7)
```
```
$ vi values.yaml
...
replicaCount: 3
...
```
```
$ helm upgrade example-app do280-repo/etherpad  -f values.yaml  (Update the configuration changes)
$ oc get po 
```


     #     Pratical   
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


```

$ helm repo add openshift-helm-charts https://charts.openshift.io/
$ helm search repo openshift-helm-charts
$ helm pull openshift-helm-charts/redhat-quarkus --untar --destination redhat-quarkus
$ helm pull openshift-helm-charts/redhat-quarkus 
```

# Check the syntax 
```
$ helm template -s templates/expense-deployment.yaml .
$ helm template -s templates/expense-route.yaml .
$ helm install --wait expense-service .
$ helm list 
$ helm get all <name>
```



```
$ helm create demo-app
$ cd  demo-app
$ helm template --skip-tests  .
$ helm template --skip-tests  . > app.yaml 
$ oc apply -f app.yaml 
```



```
$ helm get notes my-grafana -n monitoring (get the info )
```


# How to get all the helm application deployment code.
```
$ helm list -A
$ helm get manifest nfs-storage -n nfs-storage
```

