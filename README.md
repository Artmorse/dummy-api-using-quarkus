# quarkus-preview-env-demo

This project is a minimal Quarkus application.
It wants to demo how to set up preview environments using ArgoCD.

:warning: This is a dummy text to create a new commit and test ArgoCD. :bug:

## How to build the docker image

https://quarkus.io/guides/container-image#docker

:memo: In the next steps I'm prefixing the image name using `lemorse` which is my Docker Hub ID and will be used later to push the image.

```bash
mvn install -Dquarkus.container-image.build=true -Dquarkus.container-image.group=lemorse
```

Verify the image has been created.
```bash
docker images
```

Run the container.
```bash
docker run --rm -p 8080:8080 lemorse/quarkus-preview-env-demo:1.0.0-SNAPSHOT
```

Test the API.
```bash
curl http://localhost:8080/hello
```

### How to push the docker image on a registry

You can also use the [docker quarkus plugin to push the image](https://quarkus.io/guides/container-image#pushing) to your registry.

```bash
mvn verify -Dquarkus.container-image.build=true -Dquarkus.container-image.group=lemorse -Dquarkus.container-image.push=true
```

## How to generate kubernetes manifests

https://quarkus.io/guides/deploying-to-kubernetes

Adding this dependency and running `mvn verify` a `kubernetes.yml` file will be generated in the `target/kubernetes` directory.

### How to run it the application locally using kind

Create the kind cluster locally.

```bash
kind create cluster --name my-cluster
```

Create a namespace then deploy the application (service + deployment).

```bash
kubectl create ns my-quarkus
kubectl config set-context --current --namespace=my-quarkus

kubectl apply -f target/kubernetes/kubernetes.yml
````

We can verify that the service and deployment has been created.

```bash
kubectl get services
kubectl get deployments
kubectl get pods
```

You can use the port-forward kubernetes feature to bind a local port to a service port.

```bash
kubectl port-forward service/quarkus-preview-env-demo 18080:80
```

Test the API.
```bash
curl http://localhost:18080/hello
```

## How I've set up preview environments

I've created a Helm chart for the application in the [`preview` folder](preview/).

The Helm manifests are based on the kubernetes ones previously generated.

The chart will be used by ArgoCD to configure the application.

You can test it locally using:
```bash
helm install my-quarkus-app ./preview \
 --set name=quarkus-preview-env-app \
 --set username=lemorse \
 --set image=lemorse/quarkus-preview-env-demo \
 --set version=1.0.0-SNAPSHOT \
 --namespace=my-quarkus-app --create-namespace
 
helm list
```

## CI

I've configured Continuous Integration using Github Actions to automatically build docker images then push them to Docker Hub for this POC.
The workflow will be triggered on PRs only.
You can find more details looking at the [build-and-push workflow](.github/workflows/build-and-push.yml).

// TODO: manage the main build

### Configure the CI

You need to create 2 secrets in your repository:
- DOCKER_USERNAME: your docker hub registry account name
- DOCKER_PASSWORD: your docker hub password

## Setup ArgoCD application

Follow [this documentation](argocd/) to set up ArgoCD application and manage both per PR preview environment with Github notfications and staging environment (main branch).
