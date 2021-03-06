# Simple Nginx Installation

This document has instructions for installing Nginx and showing how the
built-in Kubernetes control loops work to provide declarative installation,
management and deletion semantics.

__Important note: please run the following command in a background terminal
window, so that you can see all of the commands that you run in here get 
reconciled:__

```console
kubectl get pods -n simple-nginx -w
```

## Install

A Kubernetes [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) is
the smallest unit of deployment and management in the system.

We'll install a 
[deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
to manage nginx pods. The deployment is the way that we tell Kubernetes 
controllers to manage these pods.

First, create a namespace & install the deployment:

```console
kubectl create ns simple-nginx
kubectl create -f deployment.yaml -n simple-nginx
```

## Delete

Next, delete a pod. Another pod (with a different name) should get re-created
in its place (reconciliation):

```console
kubectl delete pod $POD_NAME -n simple-nginx
```

## Scale

This installation created a deployment that then told Kubernetes to install
5 nginx pods. Let's scale up the number of pods by passing Kubernetes a new
manifest that specifies 10 replicas:

```console
kubectl apply -n simple-nginx -f deployment-scaled.yaml
```

Five _more_ nginx pods should be created by the controller, resulting in 10 
total pods running.

## Clean up

The resources we've created are organized in a hierarchy:

- The `simple-nginx` namespace contains the `nginx` deployment
    - It can contain N others as well
- The `nginx` deployment has one or more _replica sets_
- The replica sets contain one or more _pods_

We'll delete the `simple-nginx` namespace, and the controller will do all the
deletion in reverse order for us.

First, issue the delete:

```console
kubectl delete namespace simple-nginx
```

Eventually, all pods should be shut down. Finally, observe that the namespace 
is gone:

```console
kubectl get namespaces
```
