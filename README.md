### Ports

30000 - 32767

### Events

$ kubectl get events

### Logs

$ kubectl logs my-custom-scheduler --name-space=kube-system

### Set proxy if behind one

https_proxy=<my proxy> minikube start --docker-env http_proxy=<my proxy> --docker-env https_proxy=<my proxy> --docker-env no_proxy=192.168.99.0/24

### Start kube with non default driver

minikube start --vm-drive=<driver_name>

### Use docker native commands in kubernetes

eval $(minikube docker-env)

### Nodes

$ kubectl get nodes
$ kubectl describe node <node> | grep -i taint

### Access a deployment by exposing the service

kubectl expose deployment hello-minikube --type=NodePort

### Get the URL of the exposed service

$ curl $(minikube service hello-minikube --url)
$ minikube service hello-minikube --url

### Delete the deployment 

$ kubectl delete deployment hello-minikube

### Stop the local Minikube cluster

$ minikube stop

### Delete the local Minukube cluster

$ minikube delete

kubeadm join 172.31.68.187:6443 --token zbcrvz.m32clg7b3hs1tiub \
    --discovery-token-ca-cert-hash sha256:6b9d6870e24f69794d9d969e9b1f72849712b431deeda4b2e4195a1f4188cd17

### Run a Pod

$ kubectl run nginx --image=nginx

$ kubectl run redis --image=redis123 --generator=run-pod/v1

$ kubectl run --generator=run-pod/v1 nginx-pod --image=nginx:alpine

$ kubectl run --generator=run-pod/v1 redis --image=redis:alpine -l <tier=db> --> with a label

$ kubectl run --restart=Never --image=busybox static-busybox --dry-run -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml --> static 

### Edit a Pod

$ kubectl edit pod <redis>

$ kubectl get pod webapp -o yaml > my-new-pod.yaml --> extract a Pod definition

### Describe Pods

$ kubectl get pods

$ kubectl get pods -o wide

$ kubectl get pods -n kube-system -o wide

$ kubectl describe pods

$ kubectl create -f pod-definition.yml

### Scaling

$ kubectl replace -f replicaset-definition.yml

$ kubectl scale --replicas=6 -f replicaset-definition.yml

$ kubectl scale --replicas=6 replicaset myapp-replicaset

### Gen

$ kubectl get all

### Rollout

$ kubectl rollout status deployment/<myapp>
$ kubectl rollout history deployment/<myapp>
$ kubectl rollout undo deployment/<myapp>


$ kubectl create -f pod-definition.yml --record


### Services

#### Create a Service to expose <APPLICATION>

$ kubectl expose pod redis --port=6379 --name redis-service


### Deployments

#### Create a deployment and scale to 3 replicas

$ kubectl create deployment webapp --image=kodekloud/webapp-color

$ kubectl describe deployment

$ kubectl edit deployment <deployment>

$ kubectl scale deployment/webapp --replicas=3

$ kubectl expose deployment webapp --type=NodePort --port=8080 --name=webapp-service --dry-run -o yaml > webapp-service.yaml (edit .yaml file NodePort)

$ kubectl run blue --image=nginx --replicas=6 --> create a deployment with 6 replicas

### Rollout Command

$ kubectl rollout status deployment/myapp-deployment
$ kubectl set image deployment/myapp-deployment \
    nginx=nginx:1.9.1

$ kubectl rollout history deployment/myapp-deployment
$ kubectl rollout undo deployment/myapp-deployment

### Namespaces

$ kubectl get namespace 

$ kubectl get pods -n <namespace>

#### List Pods from a Namepsace

$ kubectl get pods --namespace=<NS>


### Labels and Selectors

$ kubectl get pods --selector app=App1 --> select labeled pods
$ kubectl get all --selector env=prod --> get all (e.g ReplicaSets and other objects)
$ kubectl get pods --selector env=prod,bu=finance,tier=frontend --> multiple labels

### Taints - Node

$ kubectl taint nodes node-name key=value:<taint-effect>
$ kubectl taint nodes Node1 key=blue:NoSchedule
kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule- --> remove taints

### Label Nodes

$ kubectl label nodes <node> <label-key>=<label-value>


### Affinity

### DaemonSets 

- for monitoring, logging, networking, proxy

$ kubectl get daemonsets --all-namespaces --> list all 

$ kubectl describe daemonset kube-proxy --namespace=kube-system

### ConfigMap

$ kubectl create configmap \
    <config-name> --from-literal=<key>=<value>
                  --from-literal=<key>=<value>

$ kubectl create configmap \
    <config-name> --from-file=<path-to-file>

$ kubectl create -f configMap.yaml

$ kubectl get configmaps
$ kubectl describe configmaps

### Secrets

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

### Multi-container PODs

- sidecar
- adapter
- ambassador  


### Kubetest - Tests

- e2e: ~1000