```
$ helm install --name my-ingress stable/nginx-ingress \
  --set controller.kind=DaemonSet \
  --set controller.service.type=NodePort \
  --set controller.hostNetwork=true
```

```
kubectl create -f myapp.yml
kubectl create -f myapp-ingress.yml
```

```
helm install \
    --name cert-manager \
    --namespace kube-system \
    stable/cert-manager
```

