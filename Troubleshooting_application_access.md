#### When applications are not normally reachable, stop it in OpenShift and use podman port forwarding to expose a port on the node when the container is running:

sudo podman run --name mydb -p 33060:3306 mysql

#### If this works for podman, but not OpenShift then you know you have to check your services or your routes.
#### If this also doesn't work in podman then it's something in the pod, networking, you'll have to dig a little deeper.
#### Doing this is a little limited, because it can't be done on running containers.
....................................................................................................
#### OpenShift can do port forwarding as well, to expose a port on the computer where the oc client is used:

oc port-forward mydb 33060 3306

#### Notice that this port forwarding requires you to leave the terminal where you started the port forwarding open, so SVV said you might want to run it in the background, by putting an & sign behind it or hitting control+z and continuing the command using bg.
#### The plus of using this method is it allows you to do this after the container has started and this is an OpenShift native method.
....................................................................................................
#### Troubleshooting SDN.

oc describe pod (To find the port on which the application is offering service)
oc get pods -o wide (To find the pod IP, and from the nodes that runs the pod, connect to it directly (Requires SSH access to the node)
