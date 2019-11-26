### Gen

$ kubectl get all

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
$ kubectl exec <pod> --<command> --> execute a command on the pod
$ kubectl exec <pod> -- ls /app
$ kubectl exec <pod> -- touch /app/test
$ kubectl exec <pod> -- ls /app


$ kubectl exec <pod> -c <container> -- <command> --> if there are multi containers
$ kubectl label pods <pod> mylabel=awesome --> add a new label to a pod
$ kubectl run -i --tty busybox --image=busybox --restart=Never -- sh run a shell in a pod - very useful for debugging

### Scaling Pods

- stateless
- statefull 

$ kubectl delete pod helloworld-controller-mw85

- ReplicationController is recreating a new Pod

$ kubectl scale --replicas=4 -f helloworld-replica-controller.yaml 
$ kubectl get rc
$ kubectl scale --replicas=1 rc/<replicationController>

### Deployments

- Replica Set is next-gen ReplicationController
- Deployment declaration in Kube allows you to do app deployments and updates
- When using Deployment, you define the state of your app (kube will match desired state)

- Create a Deployment
- Update a Deployment
- Do rolling update (zero downtime)
- Roll Back to a prev version
- Pause/Resume a deployment (roll-out to only a certain percentage)

$ kubectl rollout status deployment/helloworld-deployment
$ kubectl set image deployment/helloworld-deployment imageName=image:v2
$ kubectl rollout history <>
$ kubectl rollout undo <> --> rollout to prev version
$ kubectl rollout undo <> --to-revision=n --> rollout to any prev version



### Labels

- once lables are attached to an object, you can use filters to narrow down result ; this is called Label Selectors 
- using Label Selectors you can use matching expressions to match labels

- node labels, once nodes are labeled you can use label selectors to let pods only run on specific nodes
- tag the node, add a nodeSelector to your pod configuration

$ kubectl label nodes node1 hardware=high-spec
$ kubectl label nodes node2 hardware=low-spec
$ kubectl get nodes --show-labels


### Healthchecks

- run a command in a container periodically
- livenessProbes
- periodic checks on a URL (HTTP)

### readinessProbes

- livenessProbes indicate whether the container is running --> if the check fails, the container will be restarted
- readinessProbes indicate whether the container is ready to serve requests --> if the check fails, the container will not be restarted, but the Pod's IP address will be removed from the Service, so it'll not serve any requests anymore

### Pod State

- Pending
- Succeeded
- Failed
- Unknown

### Pod Lifecycle 

- initContainers:
- licecycle: postStart: preStop:

$ watch n1 kubectl get pods

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

### WebUI

$ kubectl config view

### Service Discovery (advanced) with DNS

- /etc/kubernetes/addons - on master node 
- to make DNS work, a pod will need a Service definition

$ host app1-service.default.svc.cluster.local

### ConfigMap

- expose as environment variables

### Ingress 

- inbound connection to the cluster
- IngressController


### External DNS

### Volumes

- NFS
- Cephfs
- auto provisioned volumes




###################
### Run a Pod

$ kubectl run nginx --image=nginx

$ kubectl run redis --image=redis123 --generator=run-pod/v1

$ kubectl run --generator=run-pod/v1 nginx-pod --image=nginx:alpine

$ kubectl run --generator=run-pod/v1 redis --image=redis:alpine -l <tier=db> --> with a label

$ kubectl run --restart=Never --image=busybox static-busybox --dry-run -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml --> static 

### Edit a Pod

$ kubectl get namespace 

$ kubectl get pods --namespace=<NS>

$ kubectl edit pod <redis>

$ kubectl get pod webapp -o yaml > my-new-pod.yaml --> extract a Pod definition

### Describe Pods

$ kubectl get pods

$ kubectl get pods -o wide

$ kubectl get pods -n kube-system -o wide

$ kubectl describe pods

$ kubectl create -f pod-definition.yml

### Exec

$ kubectl exec <Pod_ID> cat /log/app.log'

### Scaling Pods

- stateless
- statefull 

$ kubectl delete pod helloworld-controller-mw85
### Daemon Sets

- ensure that everys ingle pod is running on every single node


### Affinity and anti-affinity

- nodeSelector:
    hardware: high-spect 

- node and pod affnity


### Pre-populated labels

kubectl label node <nodeID> env=dev

### Rollout

- ReplicationController is recreating a new Pod

$ kubectl scale --replicas=4 -f helloworld-replica-controller.yaml 
$ kubectl get rc
$ kubectl scale --replicas=1 rc/<replicationController>

$ kubectl replace -f replicaset-definition.yml

$ kubectl scale --replicas=6 -f replicaset-definition.yml

$ kubectl scale --replicas=6 replicaset myapp-replicaset

### Services

- when using Deployments, when updaing the image version, pods are terminated and new pods take the place of older pods 
- that's why Pods hould never be accesssed directly, but always throught a Service
- a Service is a logical bridge between the `mortal` pods and other services or end-users

### Create a Service to expose <APPLICATION>

$ kubectl expose pod redis --port=6379 --name redis-service

$ kubectl describe src <service>
$ kubectl get src

### Volumes/Storage

- volumes within pod-definition
- persistent volumes (PVs)

- stateless 
- statefull

- NFS
- Cephfs

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

$ kubectl describe deployment

$ kubectl edit deployment <deployment>

$ kubectl scale deployment/webapp --replicas=3

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
### Namespaces

$ kubectl get namespace 

$ kubectl get pods -n <namespace>

#### List Pods from a Namepsace

$ kubectl get pods --namespace=<NS>


### Labels and Selectors

$ kubectl get pods --selector app=App1 --> select labeled pods
$ kubectl get all --selector env=prod --> get all (e.g ReplicaSets and other objects)
$ kubectl get pods --selector env=prod,bu=finance,tier=frontend --> multiple labels

### Affinity


### Taints - Node

$ kubectl taint nodes node-name key=value:<taint-effect>
$ kubectl taint nodes Node1 key=blue:NoSchedule
kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule- --> remove taints

### Label Nodes


$ kubectl rollout status deployment/<myapp>
$ kubectl rollout history deployment/<myapp>
$ kubectl rollout undo deployment/<myapp>

$ kubectl create -f pod-definition.yml --record
### Operators***

- packaging, deploying, and managing an Application

### DaemonSets 

- same as swarm replication_mode: Global
- for monitoring, logging, networking, proxy

$ kubectl get daemonsets --all-namespaces --> list all 

$ kubectl describe daemonset kube-proxy --namespace=kube-system

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

### Taints - Node

$ kubectl taint nodes node-name key=value:<taint-effect>
$ kubectl taint nodes Node1 key=blue:NoSchedule
kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule- --> remove taints

### Affinity

### Healthchecks

- run a command in a container periodically
- livenessProbes
- periodic checks on a URL (HTTP)

### readinessProbes

- livenessProbes indicate whether the container is running --> if the check fails, the container will be restarted
- readinessProbes indicate whether the container is ready to serve requests --> if the check fails, the container will not be restarted, but the Pod's IP address will be removed from the Service, so it'll not serve any requests anymore

### Pod State

- Pending
- Succeeded
- Failed
- Unknown

### Pod Lifecycle 

- initContainers:
- licecycle: postStart: preStop:

$ watch n1 kubectl get pods


### WebUI

$ kubectl config view

### Service Discovery (advanced) with DNS

- /etc/kubernetes/addons - on master node 
- to make DNS work, a pod will need a Service definition

$ host app1-service.default.svc.cluster.local

### Kube DNS

web-service.apps.svc.cluster.local

10.107.37.188 --> IP
cluster.local --> Root
svc --> Type
apps --> Namespace
web-service --> Hostname

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

### Ingress 

- inbound connection to the cluster\
- allows to expose services from outside to the cluster

### External DNS

- for every hostname in ingress; it will create a new record to send traffic to your loadbalancer

### Multi-container PODs

- sidecar
- adapter
- ambassador  

### Kubetest - Tests

- e2e: ~1000
- Full e2e = ~1000 Test / 12 Hours

- conformance: ~160
- Conformance = ~164 Test / 1.5 Hours

<<<<<<< HEAD
- sonobuoy
=======

---

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

### Networking

<<<<<<< HEAD
>>>>>>> 874eab21d5f88fdb690ef3a2cf4fb6c91e7e723e
=======
- CNI (Calico, Weave)
- an overlay network (flannel)

### Node Maintenance

- Node Controller --> assigns IP space, node list, health of the node
- test
<<<<<<< HEAD
- test2
=======
- test3
>>>>>>> db7f2438a2597cad61fc7e1b2c342fca477e8828
>>>>>>> a9297b727c622f3df962744fe18837e24ed743d7
