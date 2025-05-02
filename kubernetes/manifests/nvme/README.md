# THIS REPO HAS MOVED!!! Our new home: https://github.com/Altinity/antalya-examples

# Antalya Swarms using NVMe Backed Workers (Experimental)

This directory shows how to set up an Antalya swarm cluster using 
workers with local NVMe. It is still work in progress. 

The current examples show prototype configuration using the following 
options: 
* hostPath volumes
* local storage volumes

Examples apply to apply to AWS EKS only. 

## NVMe SSD provisioning. 

This is a prerequisite for either swarm type. It creates
a daemonset that automatically formats NVMe drives
on new EKS workers. The source code is currently located
[here](https://github.com/hodgesrm/eks-nvme-ssd-provisioner). It will
be transferred to the Altinity org shortly.

```
kubectl apply -f eks-nvme-ssd-provisioner.yaml
```

The daemonset has tolerations necessary to operate on swarm nodes.
Confirm that it is working by checking that a daemon appears on each new
worker node. You should also see log messages when the daemon formats
the file system on a new worker, as shown by the following example.

```
$ kubectl logs eks-nvme-ssd-provisioner-zfmv7 -n kube-system
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 228759765 4k blocks and 57196544 inodes
Filesystem UUID: fa6a9dd7-322b-456b-bc86-176c5dee2470
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
        102400000, 214990848

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done

Device /dev/nvme1n1 has been mounted to /pv-disks/fa6a9dd7-322b-456b-bc86-176c5dee2470
NVMe SSD provisioning is done and I will go to sleep now
```

The log messages are helpful if you need to debug problems with mount locations. 

## Swarm using NVMe SSD with hostPath volumes (Experimental)

The swarm-hostpath.yaml is configured to use
[hostPath](https://kubernetes.io/docs/concepts/storage/volumes/#hostpath)
volumes. 

```
kubectl apply -f swarm-hostpath.yaml
```

The local path on the worker is /nvme/disk/clickhouse. /nvme/disk is a 
soft link on the worker that points to the NVMe SSD file system mount 
point. Swarms must use a mount point below this. 

### Issues

* During autoscaling the swarm node may get scheduled onto the worker 
  before the eks-nvme-ssd-provisioner can format the disk and create 
  the mount point. In this case the pod will not correctly mount the 
  hostPath volume and will use the worker root storage instead.

## Swarm using NVMe with local persistent volumes (Experimental)

This is based on the [Local Persistence Volume Static Provisioner](https://github.com/kubernetes-sigs/sig-storage-local-static-provisioner) project. 

It attempts to create a PVs from the worker's local NVMe file system 
provisioned by the eks-nvme-ssd-provisioner. 

Local storage provisioning currently does not mesh
well with cluster autoscaling. The issue is summarized in
https://github.com/kubernetes/autoscaler/issues/1658#issuecomment-1036205889,
which also provides a draft workaround to enable the Kubernetes cluster
autoscaler to complete pod deployments when the pod depends on storage
that is allocated locally on the VM once it scales up.

### Prerequisites

* The daemonset pod spec must match the
  nodeSelector and have matching tolerations so that it can operate on
  worker nodes.

* Storage class must use a fake provisioner name or scale-up will
  not start. See 

* The EKS nodegroup must have following EKS tag set to let the autoscaler
  infer the settings on worker nodes:
  `k8s.io/cluster-autoscaler/node-template/label/aws.amazon.com/eks-local-ssd=true`

* All mount paths in the sig-storage-local-static-provisioner must
  match the eks-nvme-ssd-paths. The correct mount path is: `/pv-disks`.
  (The default path in generated yaml files is /dev/disk/kubernetes. This
  does not work.)

The included [local-storage-eks-nvme-ssd.yaml file](./local-storage-eks-nvme-ssd.yaml)
is adjusted to meet the above requirements other than the EKS tag on the swarm node 
group, which must be set manually for now. 

Install [Altinity Kubernetes Operator for ClickHouse](https://github.com/Altinity/clickhouse-operator). 
(Must be 0.24.4 or above to support CHK resource to manage ClickHouse Keeper.)

### Installation

Install as follows: 
```
kubectl apply -f local-storage-eks-nvme-ssd.yaml
```

Confirm that it is working by checking that a daemon appears on 
each new worker node. You should also see log messages when the daemon 
formats the file system, as shown by the following example. 

```
$ kubectl logs local-static-provisioner-k9rqt|more
I0330 05:10:19.357347       1 main.go:69] Loaded configuration: {StorageClass
Config:map[nvme-ssd:{HostDir:/pv-disks MountDir:/pv-disks BlockCleanerCommand
:[/scripts/quick_reset.sh] VolumeMode:Filesystem FsType: NamePattern:*}] Node
LabelsForPV:[] UseAlphaAPI:false UseJobForCleaning:false MinResyncPeriod:{Dur
ation:5m0s} UseNodeNameOnly:false LabelsForPV:map[] SetPVOwnerRef:false}
I0330 05:10:19.357403       1 main.go:70] Ready to run...
I0330 05:10:19.357464       1 common.go:444] Creating client using in-cluster
 config
I0330 05:10:19.370946       1 main.go:95] Starting config watcher
I0330 05:10:19.370964       1 main.go:98] Starting controller
I0330 05:10:19.370971       1 main.go:102] Starting metrics server at :8080
I0330 05:10:19.371060       1 controller.go:91] Initializing volume cache
I0330 05:10:19.471437       1 controller.go:163] Controller started
I0330 05:10:19.471697       1 discovery.go:423] Found new volume at host path
 "/pv-disks/fa6a9dd7-322b-456b-bc86-176c5dee2470" with capacity 921138413568,
 creating Local PV "local-pv-8312a141", required volumeMode "Filesystem"
I0330 05:10:19.481257       1 cache.go:55] Added pv "local-pv-8312a141" to ca
che
I0330 05:10:19.481326       1 discovery.go:457] Created PV "local-pv-8312a141
```

### Start swarm

The swarm-local-storage.yaml is configured to use local storage PVs. 

```
kubectl apply -f swarm-local-storage.yaml
```

For this to work you must currently scale up the node group manually to the number
of requested nodes. 

### Issues

* Autoscaling does not work, because the cluster autoscaler will hang
  waiting for acknowledgement of PVs on new workers.

* The local storage provisioner does not properly clean up PVs left
  behind when workers are deleted. This is probably a consequence of 
  using a mock
  provisioner.

* Behavior may also be flakey even when workers are
  preprovisioned. Scaling up one-by-one seems OK.
