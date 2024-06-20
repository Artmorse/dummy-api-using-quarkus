# quarkus-preview-env-demo

This project is a minimal Quarkus application.
It wants to demo how to set up preview environments using ArgoCD.

:warning: this is a dummy updates :warning:

## How to build the docker image

https://quarkus.io/guides/container-image#docker

```bash
mvn install -Dquarkus.container-image.build=true 
```

Verify the image has been created.
```bash
docker images
```

Run the container.
```bash
docker run --rm -p 8080:8080 acaron/quarkus-preview-env-demo:1.0.0-SNAPSHOT
```

Test the API
```bash
curl http://localhost:8080/hello
```

## How to generate kubernetes manifests

https://quarkus.io/guides/deploying-to-kubernetes

Adding this dependency and running `mvn verify` a `kubernetes.yml` file will be generated in the `target/kubernetes` directory.

## How to set up preview environments

I've created Helm chart for our application.

```bash
helm create preview
```

The Helm manifests are based on the kubernetes ones previously generated.

The chart will be used by ArgoCD to create preview environments.

## Configure CI

You need to create 2 secrets in your repository:
- DOCKER_USERNAME: your docker hub registry account name
- DOCKER_PASSWORD: your docker hub password

## Setup ArgoCD application

Follow [this documentation](argocd/) to set up ArgoCD application.
