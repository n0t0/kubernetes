### Gen

$ kubectl get all
$ kubectl api-resources
$ kubectl api-versions

### YAML spec

$ kubectl explain services --recursive
$ kubectl explain services.spec
$ kubectl explain services.spec.type
$ kubectl explain deployment.spec.template.spec.volumes.nfs.server
- kubernetes.io/docs/reference/#api-reference

### Pod

- a Pod describes an application running on Kubernetes
- a Pod can contain one or more tigthly coupled containers, that make up an app

### Create a Pod

$ kubectl create -f <pod-definition.yaml>

$ kubecl get pod --> info all running pods
$ kubectl describe pod <pod> --> describe
$ kubectl expose pod <pod> --port=444 --name=gaylord --> expose the port of a pod (creates a new service)
$ kubectl port-forward <pod> 8080 --> port forward the exposed pod port to your local machine
$ kubectl attach <podname> -i --> attach to the pod

$ kubectl label pods <pod> mylabel=awesome --> add a new label to a pod
$ kubectl run -i --tty busybox --image=busybox --restart=Never -- sh run a shell in a pod - very useful for debugging

### Run a Pod

$ kubectl run nginx --image=nginx
$ kubectl run redis --image=redis123 --generator=run-pod/v1
$ kubectl run --generator=run-pod/v1 nginx-pod --image=nginx:alpine
$ kubectl run --generator=run-pod/v1 redis --image=redis:alpine -l <tier=db> --> with a label
$ kubectl run --generator=run-pod/v1 tmp-shell --rm -it --image bretfisher/netshoot -- bash
$ kubectl run --restart=Never --image=busybox static-busybox --dry-run -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml --> static

### List Pods from a Namepsace

$ kubectl get namespace 
$ kubectl get pods --namespace=<NS>

### Edit a Pod

$ kubectl get namespace 
$ kubectl get pods --namespace=<NS>
$ kubectl edit pod <redis>
$ kubectl get pod webapp -o yaml > my-new-pod.yaml --> extract a Pod definition

### Describe Pod

$ kubectl get pods -w --> watch command
$ kubectl get pods
$ kubectl get pods -o wide
$ kubectl get pods -n kube-system -o wide
$ kubectl describe pods
$ kubectl create -f pod-definition.yml

### Exec

$ kubectl exec <Pod_ID> cat /log/app.log'
$ kubectl exec <pod> --<command> --> execute a command on the pod
$ kubectl exec <pod> -- ls /app
$ kubectl exec <pod> -- touch /app/test
$ kubectl exec <pod> -- ls /app
$ kubectl exec <pod> -c <container> -- <command> --> if there are multi containers

### Scaling Pods

- stateless
- statefull 

$ kubectl delete pod helloworld-controller-mw85

- ReplicationController is recreating a new Pod

$ kubectl scale --replicas=4 -f helloworld-replica-controller.yaml 
$ kubectl get rc
$ kubectl scale --replicas=1 rc/<replicationController>

### ReplicationController

- ReplicationController is recreating a new Pod

$ kubectl scale --replicas=4 -f helloworld-replica-controller.yaml 
$ kubectl get rc
$ kubectl scale --replicas=1 rc/<replicationController>
$ kubectl replace -f replicaset-definition.yml
$ kubectl scale --replicas=6 -f replicaset-definition.yml
$ kubectl scale --replicas=6 replicaset myapp-replicaset

### ReplicaSets

- better than ReplicationController
- lower than Deployment

$ kubectl scale deploy/my-apache --replicas 2
$ kubectl scale deployment my-apache --replicas 2 --> same as above

### Pod State

- Pending
- Succeeded
- Failed
- Unknown

### Pod Lifecycle 

- initContainers:
- licecycle: postStart: preStop:

$ watch n1 kubectl get pods

### Multi-container PODs

- sidecar
- adapter
- ambassador

### Logs

$ kubectl logs deployment/my-apache --follow --tail 1 --> follow logs, just last line
$ kubectl logs -l run=my-apache --> showing labeled objects logs

### Deployments

- Replica Set is next-gen ReplicationController
- Deployment declaration in Kube allows you to do app deployments and updates
- When using Deployment, you define the state of your app (kube will match desired state)

- Create a Deployment
- Update a Deployment
- Do rolling update (zero downtime)
- Roll Back to a prev version
- Pause/Resume a deployment (roll-out to only a certain percentage)

### Create a deployment and scale to 3 replicas

$ kubectl create deployment webapp --image=kodekloud/webapp-color
$ kubectl create deployment httpenv --image=bretfisher/httpenv
$ kubectl create deployment sample --image naginx --dry-run -o yaml --> dry run
$ kubectl describe deployment
$ kubectl edit deployment <deployment>
$ kubectl scale deployment/webapp --replicas=3
$ kubectl autoscale deployment/my-nginx --min=1 --max=3
$ kubectl expose deployment webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yaml (edit .yaml file NodePort)
$ kubectl run blue --image=nginx --replicas=6 --> create a deployment with 6 replicas

### Rollout Deployments

$ kubectl rollout status deployment/myapp-deployment
$ kubectl set image deployment/myapp-deployment \
    nginx=nginx:1.9.1

$ kubectl rollout history deployment/myapp-deployment
$ kubectl rollout undo deployment/myapp-deployment

$ kubectl rollout status deployment/helloworld-deployment
$ kubectl set image deployment/helloworld-deployment imageName=image:v2
$ kubectl rollout history <>
$ kubectl rollout undo <> --> rollout to prev version
$ kubectl rollout undo <> --to-revision=n --> rollout to any prev version

### Services

- when using Deployments, when updaing the image version, pods are terminated and new pods take the place of older pods 
- that's why Pods hould never be accesssed directly, but always throught a Service
- a Service is a logical bridge between the `mortal` pods and other services or end-users

### Service Type

- ClusterIP (default) --> reachable inside the cluster only
- NodePort --> outside the cluster, through IPs on the nodes themselves
- LoadBalancer (cloud)
- ExternalName --> adds CNAME DNS record to CoreDNS only

- Ingress

### Create a Service to expose <APPLICATION>

$ kubectl expose pod redis --port=6379 --name redis-service
$ kubectl expose deployment/httpenv --port 8888
$ kubectl expose deployment/httpenv --port 8888 --name httpenv-np --type NodePort
$ kubectl expose deployment/httpenv --port 8888 --name httpenv-lb --type LoadBalancer

$ kubectl describe src <service>
$ kubectl get src

### Service Discovery (advanced) with DNS

- /etc/kubernetes/addons - on master node 
- to make DNS work, a pod will need a Service definition

$ host app1-service.default.svc.cluster.local

### Kube DNS

<hostname>.<namespace>.svc.cluster.local
web-service.apps.svc.cluster.local

10.107.37.188 --> IP
cluster.local --> Root
svc --> Type
apps --> Namespace
web-service --> Hostname

### External DNS

- for every hostname in ingress; it will create a new record to send traffic to your loadbalancer

### Volumes/Storage

- volumes within pod-definition
- persistent volumes (PVs)

- stateless 
- statefull

- NFS
- Cephfs
- auto provisioned volumes

### Labels

- once lables are attached to an object, you can use filters to narrow down result ; this is called Label Selectors 
- using Label Selectors you can use matching expressions to match labels
- node labels, once nodes are labeled you can use label selectors to let pods only run on specific nodes
- tag the node, add a nodeSelector to your pod configuration

$ kubectl label nodes node1 hardware=high-spec
$ kubectl label nodes node2 hardware=low-spec
$ kubectl get nodes --show-labels
$ kubectl label nodes <node> <label-key>=<label-value>

### Labels and Selectors

$ kubectl get pods --selector app=App1 --> select labeled pods
$ kubectl get all --selector env=prod --> get all (e.g ReplicaSets and other objects)
$ kubectl get pods --selector env=prod,bu=finance,tier=frontend --> multiple labels

### Pre-populated labels

$ kubectl label node <nodeID> env=dev

### Healthchecks

- run a command in a container periodically
- livenessProbes
- periodic checks on a URL (HTTP)

### Affinity and anti-affinity

- nodeSelector:
    hardware: high-spect 

- node and pod affnity

### readinessProbes

- livenessProbes indicate whether the container is running --> if the check fails, the container will be restarted
- readinessProbes indicate whether the container is ready to serve requests --> if the check fails, the container will not be restarted, but the Pod's IP address will be removed from the Service, so it'll not serve any requests anymore

### Secrets

- external vault services
- use secrets as environment variables
- use secrets as a file in a pod
- use an external image to pull secrets (from private image registry)


$ echo -n "root" > ./username.txt
$ echo -n "password" > ./password.txt
$ kubectl create secret generic db-user-pass --from-file=./username.txt --from-file=./password.txt 

$ kubectl create secret generic ssl-certificate --from-file=ssh-privatekey=~/.ssh/id_rsa --ssl-cert-=ssl-cert=mysslcert.crt

- you can create a pod that exposes the secrets as environment variables
- provite secrets in a secrets file

$ kubectl -n kube-system get secret

- imperative

$ kubectl create secret generic \
    <secret-name> --from-literal=<key>=<value>
$ kubectl create secret generic \
    <secret-name> --from-file=<path-to-file>

- declerative

$ kubectl create -f 

- Encode Secrets and Decode

$ echo -n 'passwrd' | base64
$ echo -n 'bXafmjagh2D=' | base64 --decode

- List Secrets 

$ kubectl get secrets
$ kubectl get secret <secret-name> -o yaml
$ kubectl describe secret

### ConfigMap

- expose as environment variables
- inject configuration settings into containers without changing the container itself

$ kubectl create configmap app-config --from-file=app.properties
$ kubectl get configmap 
$ kubectl create configmap \
    <config-name> --from-literal=<key>=<value>
                  --from-literal=<key>=<value>
$ kubectl create configmap \
    <config-name> --from-file=<path-to-file>
$ kubectl create -f configMap.yaml
$ kubectl get configmaps
$ kubectl describe configmaps

### DaemonSets 

- ensure that every single pod is running on every single node
- same as swarm replication_mode: Global
- for monitoring, logging, networking, proxy

$ kubectl get daemonsets --all-namespaces --> list all 
$ kubectl describe daemonset kube-proxy --namespace=kube-system

### WebUI

$ kubectl config view

### RBAC - role based access control

- webhook --> sends autorhozation request to an exnternal REST interface

- role
- roleBinding
- clusterRole
- clusterRoleBinding

$ kubectl config view
$ kubectl config set-context --cluster=*.* --user edward
$ kubectl config use-context edward
$ kubectl config get contexts 

### Ingress 

- inbound connection to the cluster
- IngressController

### External DNS

### Networking

- CNI (Calico, Weave)
- an overlay network (flannel)

### Node Maintenance

- Node Controller --> assigns IP space, node list, health of the node

### TLS on AWS ELB

 - annotations

### Admission Controllers

- intercept requests setn to kubernetes API server

$ kube-apiserver --enable-admission-plugins=NamespaceLifecycle,...

- NamespaceLifecycle
- LimitRanger
- ServiceAccount
- DefaultStorageClass
- DefaultTolerationSeconds
- NodeRestriction
- MutatingAdmissionWebhook
- ValidatingAdmissionWebhook

### Pod Security Policies

- deny using privileged mode in pods
- control what volumes can be mounted
- containers can't run as root but within <UID/GID> range

$ kubectl edit cluster
$ kubectl get podsecuritypolicy

### etcd

- used by kubernetes as data backend
- k/v store

### Raft consensus algorithm

https://raft.github.io

### Packaging and Deploying

### Helm

- package manager for kubernetes
- a chart is a collection of files that describe a set of kubernetes resources
- charts use templates that are typicaly developed by a package maintainer

```
$ helm init 
$ helm reset
$ helm install
$ heml search --> search for a chart
$ helm list
$ helm upgrade 
$ helm rollback
```

### Helm Charts

- helm allows for upgrades and rollbacks
- helm charts are version controlled

```
$ helm create *mychart* --> creates a directory /mychart
```

### Helm Chart repository using AWS S3

- create a .sh script

### Building and Deploying a Helm Chart with Jenkins

- aws roles to read from s3 and deploy to cluster
- serviceAcccount
- build pipeline script
- deploy pipeline sciprt

### Skaffold

- command line tool for continuous development of applications running on kubernetes
- skaffold will build, push, and deploying to kubernetes cluster
- very pluggable - builds with Kaniko and Bazel or custom builds
- for developeres and me

### Serverless in Kubernetes

- OpenFaas
- Kubeless**
    - HTML functions
    - Scheduled
    - PubSub (kafka)
    - AWS Kinesis
- Fission
- OpenWhisk

### Taints - Node

$ kubectl taint nodes node-name key=value:<taint-effect>
$ kubectl taint nodes Node1 key=blue:NoSchedule
kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule- --> remove taints

### Operators***

- packaging, deploying, and managing an Application

### Cert-manager

helm install --name my-ingress stable/nginx-ingress \
             --set controller.kind=DaemonSet \
             --set controller.service.type=NodePort \
             --set controller.hostNetwork=true

