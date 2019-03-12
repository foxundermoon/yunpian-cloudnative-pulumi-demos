[![Deploy](https://get.pulumi.com/new/button.svg)](https://app.pulumi.com/new)

# Kubernetes Guestbook (with Components)

A version of the [Kubernetes Guestbook](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)
application using Pulumi. Unlike [the straight port of the original YAML](../simple), this variant
leverages real code to eliminate boilerplate. A `ServiceDeployment` class is used that combines the common pattern
of deploying a container image using a Kubernetes `Deployment`, and then scaling it using a `Service`.

## Running the App

Follow the steps in [Pulumi Installation](https://pulumi.io/install/) and [Kubernetes Setup](https://pulumi.io/quickstart/kubernetes/setup.html) to get Pulumi working with Kubernetes.

Install dependencies:

```sh
npm install
```

Create a new stack:

```sh
$ pulumi stack init k8s-guestbook-components

Created stack 'k8s-guestbook-components'
```

This example will attempt to expose the Guestbook application to the Internet with a `Service` of
type `LoadBalancer`. Since minikube does not support `LoadBalancer`, the Guestbook application
already knows to use type `ClusterIP` instead; all you need to do is to tell it whether you're
deploying to minikube:

```sh
pulumi config set isMinikube <value>
```

Perform the deployment:

```sh
$pulumi up       19:37   19-12-03  
Previewing update (k8s-guestbook-components):

     Type                                Name                                     Plan        Info
     pulumi:pulumi:Stack                 guestbook-easy-k8s-guestbook-components
     └─ k8sjs:service:ServiceDeployment  frontend
 +-     └─ kubernetes:core:Service       frontend                                 replace     [diff: ~metadata,spec]

Resources:
    +-1 to replace
    9 unchanged

Do you want to perform this update? yes
Updating (k8s-guestbook-components):

     Type                                Name                                     Status       Info
     pulumi:pulumi:Stack                 guestbook-easy-k8s-guestbook-components
     └─ k8sjs:service:ServiceDeployment  frontend
 +-     └─ kubernetes:core:Service       frontend                                 replaced     [diff: ~metadata,spec]

Outputs:
  + frontendIp: "10.106.212.163"

Resources:
    +-1 replaced
    9 unchanged

Duration: 16s

Permalink: https://app.pulumi.com/foxundermoon/guestbook-easy/k8s-guestbook-components/updates/2
```

And finally - open the application in your browser to see the running application. If you're running
macOS you can simply run:

```sh
open $(pulumi stack output frontendIp)
```

> _Note_: minikube does not support type `LoadBalancer`; if you are deploying to minikube, make sure
> to run `kubectl port-forward svc/frontend 8080:80` to forward the cluster port to the local
> machine and access the service via `localhost:8080`.

![Guestbook in browser](./imgs/guestbook.png)
