# app-router-kubernetes
internal application routing between microservices managed by nginx

### What is service discovery ?
Service discovery is a method that enables services to discover one another dynamically without the requirement for endpoint configuration or IP addresses to be hard-coded.

Microservices are used in current cloud-native infrastructures like Kubernetes to create applications. Applications require communication across the various parts of a microservices architecture, yet individual IP addresses and endpoints are dynamic.

Issue
Most users save mapping into configmap and inject it into deployment, or they hardcode service name mapping to each deployed microservice in Kubernetes.

The worst-case scenario is when a single service name needs to be updated and a restart of all microservices is necessary as a result of configmap being injected into every deployment or having a hardcoded value.


Nginx managing internal traffic

When many teams are working on the same project, integrating nginx inside Kubernetes to manage the east-west traffic would be quite beneficial and increase flexibility.

Demo

Let’s first deploy the app-router

Open and edit the nginx-configmap.yaml file from the above GitHub link.

Edit the location block and service names as per need in configmap.

else you can deploy the existing demo files from the demo folder.

```
kubectl apply -f ./demo
```

Once our app-router is up & running let’s create the demo services to verify the changes.

```
kubectl create deployment hello-node --image=registry.k8s.io/echoserver:1.4
kubectl create deployment hello-node-v2 --image=us-docker.pkg.dev/google-samples/containers/gke/hello-app:1.0
kubectl expose deployment hello-node --port=8080
kubectl expose deployment hello-node-v2 --port=8080
```
Above command will create the two demo deployments and services.


now to test routing with app-router we will create one demo POD or alpine to test the curl.

```
kubectl run -i --tty --rm debug --image=alpine --restart=Never -- sh
```

install the curl by running the command :

```
apk add curl
```
moment of truth, let's test routing and curl the app-router and verify if it’s forwarding the requests to demo services or not.

<img width="754" alt="Screenshot 2022-11-12 at 2 06 53 AM" src="https://user-images.githubusercontent.com/15871000/201431435-915a9838-2fc1-47e2-9609-a2fe98b2f583.png">



The requests are then forwarded by hitting the app-router with the path configuration that we have provided.

Using app-router, the two demo services could communicate with one another, and in the event of a service update, we would manage it at a single app-router configuration place.

Instead of using custom Nginx deployment as app-router, Nginx ingress controller also could be used as an internal ingress setup. There are also other service discovery tools like Eureka which could be leveraged based on requirements.
