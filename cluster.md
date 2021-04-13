# Chapter 2: Set up your cluster

## Prerequisites

First we will install some tools. [Kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) is the Kubernetes CLI tool, [kind](https://kind.sigs.k8s.io/) is used for running a Kubernetes cluster locally on your own computer, and [Flux](https://toolkit.fluxcd.io/) is a tool for running [GitOps](https://www.gitops.tech/#what-is-gitops) – deploying changes to your cluster by making changes to a git repo.

    brew update
    brew install kind
    brew install kubectl
    brew install fluxcd/tap/flux

Optional tools. `watch` is used to watch commands and [stern](https://github.com/wercker/stern) is used for being able to tail pod logs easily.

    brew install watch
    brew install stern

## Create a local Kubernetes cluster

We will use kind to set up a Kubernetes locally on your own computer.

Create a [Github personal access token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) with all `repo` permissions enabled.

Set environment variables (for instance in `~/.bashrc` or `~/.zshrc`):

    export GITHUB_TOKEN=<your-token>
    export GITHUB_USER=<your-username>

Create your first cluster!

    kind create cluster

Verify that everything worked OK.

    kubectl cluster-info
    flux check --pre

You now have a fully functional Kubernetes cluster running on your computer! Continue to [chapter 3](./pod.md) to add your first pod to the cluster.
