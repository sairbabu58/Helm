# Create your own helm repo and install the application

**from github, create a repo 'helmrepo'**

**clone the url to your local machine**
```
$ git clone https://github.com/name/helmrepo
$ cd helmrepo
```
```
$ helm create demochart
$ helm package demochart
$ helm repo index .
```
```
$ git add .
$ git commit -m 'updated 1st chart'
$ git push 
```
```
> add additional configuration on gitrepo
  > choose your helmrepo 
  > setting 
  > pages
  > source (none to master)
  > save
  > get the helm new repo url (it will display over the screen)
 ``` 
```
$ helm repoadd gitrepo <paste the above url>
$ helm repo list
$ helm install myapp gitrepo/helmrepo
```

# Helm OCI registry
```
$ docker run -d --name oci-registry -p 5000:5000 registry
```
```
$ helm package firstchart
$ helm push firstchart-0.1.0.tgz oci://localhost:5000/helm-charts
$ helm show all oci://localhost:5000/helm-charts/firstchart --version 0.1.0
$ helm pull oci://localhost:5000/helm-charts/firstchart --version 0.1.0
$ helm template myrelease oci://localhost:5000/helm-charts/firstchart --version 0.1.0
$ helm install myrelease oci://localhost:5000/helm-charts/firstchart --version 0.1.0
$ helm upgrade myrelease oci://localhost:5000/helm-charts/firstchart --version 0.2.0
$ helm registry login -u myuser <oci registry>
$ helm registry logout <oci registry url>
```


