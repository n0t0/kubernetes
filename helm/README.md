### Repo

$ helm repo list --> list repos
$ helm repo add dev https://example.com/dev-charts
$ helm repo update
$ helm search repo <repo> --> search repos
$ helm search repo stable
$ helm search repo --> search all charts in all added repos
$ helm search hub --> search dozens of different repos
$ helm search hub elasticsearch --> search elastic in all repos

### Charts

$ helm show chart elastic/elasticsearch --> show info about the chart
$ helm show all elastic/elasticsearch
$ helm show values <chart>

### Install a Chart

$ helm install stable/mysql --generate-name --> will generate random name
$  helm upgrade --install <release name> --values <values file> <chart directory> --> install-or-upgrade

### Releases

$ helm list
$ helm ls

### Uninstall a Release

$ helm uninstall <name>
$ helm uninstall mongodb

### Getting Help

$ helm get -h

### Get Command

$ helm get manifest <release> --> download the YAML
$ helm get values <release> --> download the values

### Status

$ helm status <chart>

### Passing Values

$ helm install <name> <chart> --set nodeSelector."kubernetes\.io/role"=master

### Helm Upgrade and Helm Rollback

$ helm upgrade -f panda.yaml happy-panda stable/mariadb
$ helm rollback happy-panda 1 --> rolls back to the first release
$ helm history elk-coord

### Create Charts

$ helm create <name>
$ helm package test_chart
$ helm install test_chart ./test_chart-0.1.0.tgz




$ helm inspect -- helm show
$ helm fetch -- helm pull
