[![Deploy](https://get.pulumi.com/new/button.svg)](https://app.pulumi.com/new)

# Kubernetes Guestbook (Simple Variant)

A version of the [Kubernetes Guestbook](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)
application using Pulumi.

This is a straight port of the original YAML, and doesn't highlight advantages of using real languages. For an example
using abstraction to cut down on boilerplate, please see the [variant using components](../components),
also in this repo. It provisions the same set of resources.

## Running the App

Follow the steps in [Pulumi Installation](https://pulumi.io/install/) and [Kubernetes Setup](https://pulumi.io/quickstart/kubernetes/setup.html) to get Pulumi working with Kubernetes.

Install dependencies:

```sh
npm install

#or

yarn
```

Create a new stack:

```sh
pulumi stack init k8s-guestbook

Created stack 'k8s-guestbook'
```

This example will attempt to expose the Guestbook application to the Internet with a `Service` of
type `LoadBalancer`. Since minikube does not support `LoadBalancer`, the Guestbook application
already knows to use type `ClusterIP` instead; all you need to do is to tell it whether you're
deploying to minikube:

```sh
pulumi config set isMinikube true
```

Perform the deployment:

```sh
pulumi up                                                             
Previewing update (k8s-guestbook):

     Type                           Name                     Plan
 +   pulumi:pulumi:Stack            guestbook-k8s-guestbook  create
 +   ├─ kubernetes:apps:Deployment  redis-replica            create
 +   ├─ kubernetes:apps:Deployment  redis-master             create
 +   ├─ kubernetes:apps:Deployment  frontend                 create
 +   ├─ kubernetes:core:Service     redis-replica            create
 +   ├─ kubernetes:core:Service     redis-master             create
 +   └─ kubernetes:core:Service     frontend                 create

Resources:
    + 7 to create

Do you want to perform this update? yes
Updating (k8s-guestbook):

     Type                           Name                     Status       Info
     Type                           Name                     Status
 +   pulumi:pulumi:Stack            guestbook-k8s-guestbook  created
 +   ├─ kubernetes:apps:Deployment  redis-master             created
 +   ├─ kubernetes:apps:Deployment  redis-replica            created
 +   ├─ kubernetes:apps:Deployment  frontend                 created
 +   ├─ kubernetes:core:Service     redis-master             created
 +   ├─ kubernetes:core:Service     redis-replica            created
 +   └─ kubernetes:core:Service     frontend                 created

Outputs:
    frontendIp: "10.105.132.145"

Resources:
    + 7 created

Duration: 3m11s

Permalink: https://app.pulumi.com/foxundermoon/guestbook/k8s-guestbook/updates/1
```

And finally - open the application in your browser to see the running application. If you're running
macOS you can simply run:

```sh
open $(pulumi stack output frontendIp)
```

> _Note_: minikube does not support type `LoadBalancer`; if you are deploying to minikube, make sure
> to run `kubectl port-forward svc/frontend 8080:80` to forward the cluster port to the local
> machine and access the service via `localhost:8080`.
> or `minikube ssh "socat TCP4-LISTEN:8080,reuseaddr,fork TCP4:$(pulumi stack output frontendIp):80"` forword by socat, then access by  `minikube ip` minikube ip address, sutch as `192.168.64.7:8080`

![Guestbook in browser](./imgs/guestbook.png)
