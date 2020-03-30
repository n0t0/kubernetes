### Install GlusterFS

https://wiki.centos.org/SpecialInterestGroup/Storage/gluster-Quickstart

### Install GlusterFS-client

$ sudo yum install glusterfs-fuse

### Adding a Server

$ gluster peer probe <server>

### Listing Pool

$ gluster pool list

### Viewing Peer Status

$ gluster peer status


### Configure Heketi

https://github.com/heketi/heketi/blob/master/docs/admin/install-kubernetes.md

### Insert Module

$ modprobe dm_snapshot
$ yum install -y rdma-core-devel readline-devel libaio-devel

### Install Heketi

$ sudo ./gk-deploy  --admin-key --user-key user


### ENV

export HEKETI_CLI_USER=admin
export HEKETI_CLI_KEY=admin
heketi-cli topology info
Cluster Id: 1322d4b64ad27d6f2c4c47a3bbdd1b14...

unset HEKETI_CLI_USER HEKETI_CLI_KEY
heketi-cli topology info
Error: Invalid JWT token: Token missing iss claim


wget -qO- http://heketi.heketi.svc.cluster.local:8080/volumes
{"volumes":["85516baa0869f946ca492cc941ca2b76"]}

### Troubleshoot on OpenShift

$ oc project app-storage
$ oc rsh heketi-storage-1-r72v7
$ heketi-cli topology info