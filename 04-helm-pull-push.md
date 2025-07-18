# Helm registry login, pull, push 
https://artifacthub.io/packages/helm-plugin/push-artifactory-plugin/push-artifactory

1) Create helm chart and push to jforg server
2) Pull/download helm chart .tar file from internet or official side
3) Push helm chart .tgz file to jforg server
4) Pull helm chart from jforg server and run your application 



# Process:
# create helm new sub repo under k8s-helm-lab directory
```
 $ helm registry login https://jforg.example.com
     username:
     password:
```
```
(optional)
 $ helm repo add k8s https://jforg.example.com/k8s --username admin --password redhat #add the new repo on jforg server.
     note: 1st jforg team need to create a helm repo on server: k8s-helm-lab
                                                                k8s-helm-dev
                                                                k8s-helm-prod
    out put of  helm repo add command :                         k8s-helm-lab/k8s
                                                                k8s-helm-lab/ocp
```
```
 $ helm repo list 
 $ helm repo update
```

# create your own helm chart
```
   $ helm create my-app-01
   $ tree my-app-01 (#if require, modify the file)
   $ helm package my-app-01 (#it will create tgz file. my-app-01-0.1.0)
```
```
   $ helm lint file.tgz #validate whether all the codes are correct or not
```
```
   $ helm package filename --version v2.0.2
```

# download chart from internate
example: 
```
      $ helm repo list (# existing repo list and url details)
      $ open the above url over browser and download the .tgz file
      $ if above .tgz file having all the source code, please find the chart directory or any helm related dir and create package
      $ go to the above helm or chart path execute below command
      $ helm package <filename>
```
# Now .tgz file is ready, lets push to jforg server
```
    $ helm plugin install https://github.com/belitre/helm-push-artifactory-plugin --version v0.3.0 (# Optional)
    $ helm push-artifactory my-app-01-0.1.0.tgz https://jforg.example.com/k8s-helm-lab/k8s -u admin -p redhat
    $ validation: open jforg server-> refresh page-> select your repo(k8s-helm-lab/k8s)-> my-app-01-> tgz file
```
```
optional: curl -uadmin:redhat -T my-app-01-0.1.0 https://jforg.example.com/k8s-helm-lab/k8s
```

**Note: https://jfrog.com/help/r/jfrog-artifactory-documentation/kubernetes-helm-chart-repositories**

# Validate using helm cli
```
    $ helm repo list
    $ helm repo update
    $ helm search --help
    $ helm search repo
    $ helm search repo | grep my-app-01
```
**Note: if is it not visable, please check with jforg admin team and get the permission**

# Pulling helm chart and deploy application on k8s cluster
```
    $ kc get no
    $ kc create ns demo
    $ helm install demo-app k8s-helm-lab/my-app-01 (#helm search repo. it will show the repo path)
    $ kc get po -n demo

```



# Ansible automation for pull helm chart from internet
```
1) get the right path for helm chart
2) test the chart localy
3) get the chart .tar.gz file
4) download the .tar.gz file to your localmachine
5) push the helm chart .tar.gz file
6) test the code and run the application
```
# Process:
```
1) manually add the helm repo and check the code whether everyting working as expected
$ helm repo add cilium https://helm.cilium.io/
$ helm install cilium cilium/cilium --namespace=kube-system
$ helm repo list 
2) get the helm url and repo name
3) use those details over ansible playbook
```
or
```
1) search and get the helm repo url
https://artifacthub.io/packages/helm/cilium/cilium
2) use those details on ansible playbook
```


# Ansible playbook:
```
$ ansible-doc kubernetes.core.helm_pull

- name: Download helm chart for cilium
  kubernetes.core.helm_pull:
    chart_ref: cilium
    repo_url: https://helm.cilium.io/
    untar_chart: no
    destination: /root/helm-repo
    skip_tls_certs_check: yes
```
```
- name: Download helm chart for redis
  kubernetes.core.helm_pull:
    chart_ref: redis
    repo_url: https://charts.bitnami.com/bitnami
    untar_chart: no
    destination: /root/helm-repo
    skip_tls_certs_check: yes
```
```
- name: Download helm chart for ingress
  kubernetes.core.helm_pull:
    chart_ref: ingress-nginx
    repo_url: https://kubernetes.github.io/ingress-nginx
    untar_chart: no
    destination: /root/helm-repo
    skip_tls_certs_check: yes

```

# now download the helm chart .tar.gz file
# then push helm chart .tar.gz file to jforg server 


**Note: adding repo url name is very trick work. always get the exactly chart name using below process.**
```
$ manually add the helm repo
$ helm repo add netapp https://netapp.github.io/trident-helm-chart
$ helm repo list
$ helm repo update
$ helm search repo netapp
$ here you will get exactly chart name netapp-trident or netapp-operator or anything else.
```
So please copy the right name from here and use same chart name and repo url on ansible playbook and pull the .tar.gz helm chart




# New Code: Ansible playbook.



```
$ cat helm-chart.yaml 
---
#Helm Charts Pulling from Internet


- include_vars:
    file: vars.yaml
    name: helm_chart

- name: create a local directory
  ansible.builtin.file:
    path: $HOME/repodir/{{ item.chart_name }}
    state: directory
  with_items:
    - "{{ helm_chart.helm_charts }}"

- name: Download  helm chart
  kubernetes.core.helm_pull:
    chart_ref: "{{ item.chart_name }}"
    repo_url: "{{ item.helm_url }}"
    chart_version: "{{ item.chart_version }}"
    untar_chart: no
    destination: $HOME/repodir/{{ item.chart_name }}
    skip_tls_certs_check: yes
  with_items:
    - "{{ helm_chart.helm_charts }}"

#Helm Chart push to Artifactory
#

#- name: Find files in the directory
#  ansible.builtin.find:
#    paths: $HOME/repodir/
#    patterns: "*.tgz"
#    file_type: file
#    recurse: yes
#  register: found_files

#- name: setfact
#  ansible.builtin.set_fact:
#    file_names:  "{{ found_files.files | map(attribute='path') | map('basename') | join (' ')  }}"

#- name: Display filenames as debug output
#  ansible.builtin.debug:
#    var: file_names

- name: Configure platform
  ansible.builtin.shell: jf config add artifactory  --url  https://artifactory.j2c.com/ --user "{{ username }}" --password "{{ password }}" --interactive=false
  ignore_errors: true

- name: Push helm chart to jfrog server
  ansible.builtin.shell:
     cmd: jf rt u {{ item.chart_name }}/  helm-{{ env }}-virtual/ckp/
     chdir: $HOME/repodir/
  with_items: 
    - "{{ helm_chart.helm_charts }}"
  ignore_errors: true

- name: delete the local directory
  ansible.builtin.file:
    path: $HOME/repodir
    state: absent

- name: Remove platform
  ansible.builtin.shell: jf config rm artifactory --quiet

```
```
$ vi vars.yaml

helm_charts:
  - chart_name: cilium
    helm_url: https://helm.cilium.io/
    chart_version: "{{ cilium_chart_version }}"
  - chart_name: argo-cd
    helm_url: https://argoproj.github.io/argo-helm
    chart_version: "{{ argocd_chart_version }}"
  - chart_name: cert-manager
    helm_url: https://charts.jetstack.io
    chart_version: "{{ cert_manager_chart_version }}"

```

