# tech-trends
Trends in tech

- `Vagrant up`
- `vagrant ssh`


- Inside the virtual machine(vagrant)
 `sudo su -`

# IMPORTANT
- Install apparmor-parser. This will be necessary for the deployed containers in the latter steps to run properly
`zypper install apparmor-parser`

- Launch a  k3s kubernetes cluster
- `curl -sfL https://get.k3s.io | sh -`

Check the nodes available

# Install Argo CD: Create namespace and deploy
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```
NOTE for me: I like downloading remote file, inspect and apply locally.
e.g 
```
curl -L https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml > argocd-application.yaml
```

Go to vagrant home where you can see the copied files
`cd /home/vagrant`

## On local machine
If ssh key is already generated for local machine and you want to create a new one:
`vi ~/.ssh/known_hosts`

Delete the old ssh key

Copy all argocd files from local machine to the virtual machine

- For helm
  
- `scp -r -P 2222 $PWD/argocd-helm   vagrant@localhost:.`

For Kustomize

```sh
scp -r -P 2222 $PWD/argocd-kustomize  vagrant@localhost:.
```

# Expose ArgoCD application to the browser from the virtual machine

```sh
kubectl apply -f argocd-kustomize/server-nodeport.yaml 
```

- Can now be viewed at `https://192.168.50.4:30007/` or `https://192.168.50.4:30007/`
aside: 192.168.50.4 is the IP of the virtual machine.(can be seen in Vagrantfile)

### Login to the UI

Username: admin
You can get the password by:
```sh
  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
  password-


### Launch All the the cluster as a single application
`kubectl apply -f argocd-kustomize/tech-trends-all.yaml`

You can also launch deploy them separately for different environments
`kubectl apply -f argocd-kustomize/tech-trends-sandbox.yaml`
`kubectl apply -f argocd-kustomize/tech-trends-staging.yaml`
`kubectl apply -f argocd-kustomize/tech-trends-production.yaml`

of simply by apply the argocd-kustomize folder:
`kubectl apply -f argocd-kustomize/`





### Note for me 
sh into pod in namespace dev
`kubectl exec --stdin --tty <pod-name> -n dev -- /bin/bash`

`kubectl exec --stdin --tty tech-trends-web-dev-5d5b9cc7f-ndmsd -n dev -- /bin/bash`
`kubectl exec --stdin --tty tech-trends-web-dev-5d5b9cc7f-gpdwv -n dev -- /bin/bash`

kubectl get po -n dev
