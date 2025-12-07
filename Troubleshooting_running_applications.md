### Troubleshooting running applications.

#### Gives an overview of cluster-level events.

oc get events

#### For an overview on a specific resource

oc describe <resource>

#### The go to resource with errors in the running app is the pod.

oc describe pod <podname>

#### But you can also look at other related resources.

oc describe <storage> 
oc describe <network>

#### Sometimes "oc logs" doesn't show the issue.

oc logs <podname>





