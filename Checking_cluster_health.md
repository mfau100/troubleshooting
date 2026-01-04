### Checking cluster health.
1. oc get co is the primary high-level health view of an OpenShift cluster, but with an important nuance.
- What oc get co actually shows - co = ClusterOperators - Each row represents an operator responsible for a core cluster component (API server, etcd, networking, ingress, etc.). 
- So this command answers: Are the core parts of the OpenShift control plane healthy and working as intended?
2. How to read the columns.
- Key indicators:
- AVAILABLE=True	Component is working and serving traffic
- PROGRESSING=False	Not currently rolling out changes or stuck updating
- DEGRADED=False	No known failures

[mfau@mfau-thinkpadx1carbon5th ~]$ oc get co
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE   MESSAGE
authentication                             4.18.2    True        False         False      35m
config-operator                            4.18.2    True        False         False      293d
console                                    4.18.2    True        False         False      40m
control-plane-machine-set                  4.18.2    True        False         False      293d
dns                                        4.18.2    True        False         False      40m
etcd                                       4.18.2    True        False         False      293d
image-registry                             4.18.2    True        False         False      40m
ingress                                    4.18.2    True        False         False      293d
kube-apiserver                             4.18.2    True        False         False      293d
kube-controller-manager                    4.18.2    True        False         False      293d
kube-scheduler                             4.18.2    True        False         False      293d
kube-storage-version-migrator              4.18.2    True        False         False      40m
machine-api                                4.18.2    True        False         False      293d
machine-approver                           4.18.2    True        False         False      293d
machine-config                             4.18.2    True        False         False      293d
marketplace                                4.18.2    True        False         False      293d
network                                    4.18.2    True        False         False      293d
openshift-apiserver                        4.18.2    True        False         False      40m
openshift-controller-manager               4.18.2    True        False         False      36m
openshift-samples                          4.18.2    True        False         False      293d
operator-lifecycle-manager                 4.18.2    True        False         False      293d
operator-lifecycle-manager-catalog         4.18.2    True        False         False      293d
operator-lifecycle-manager-packageserver   4.18.2    True        False         False      40m
service-ca                                 4.18.2    True        False         False      293d

### Does this mean the cluster is healthy? Yes, at the control plane level
- API servers
- etcd
- networking
- ingress
- authentication
- operators

### This is exactly what OpenShift support or SREs check first.

### What it does not fully cover - oc get co does not tell you:

- Application workload health
- Whether user pods are crashing
- Node disk pressure / memory exhaustion (unless it impacts operators)
- App-level issues in namespaces
..........................................................................
### Complementary health checks

