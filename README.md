# kubernetes-tutorial

This is a guide for getting started with Kubernetes! During the tutorial, we will begin with setting up a Kubernetes cluster locally on your own computer using [kind](https://kind.sigs.k8s.io/docs/user/quick-start/). Next we will use [Flux](https://toolkit.fluxcd.io/get-started/) to deploy your first pod into the cluster. Finally we will take a look at different options from accessing the code running inside your pod from the outside.

The guide is written with MacOS in mind. Most of the commands should be easily transposable to other operating systems as well though.

- [Chapter 1: Introduction](./introduction.md): If you are totally new to Kubernetes, the introduction will gently introduce you to some of the core concepts.
- [Chapter 2: Create your Kubernetes cluster](./cluster.md): In this chapter we will use [kind](https://kind.sigs.k8s.io/docs/user/quick-start/) to create a Kubernetes cluster running locally on your computer.
- [Chapter 3: Deploy your first pod](./pod.md): Using [Flux](https://toolkit.fluxcd.io/get-started/), we will inject a pod into the cluster.
- [Chapter 4: Add an ingress to the pod](./ingress.md): In the final chapter, we will set up NGINX in order to route traffic properly into the cluster.
