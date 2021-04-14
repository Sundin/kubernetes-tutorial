# Chapter 2: Set up your cluster

In this chapter you will launch your own Kubernets cluster, locally on your own computer! Note that if you are not running MacOS or do not have [Homebrew](https://brew.sh/) installed, you need to find out alternative installation methods for the tools described below.

## Prerequisites

First we will install some tools. [Kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) is the Kubernetes CLI tool, [kind](https://kind.sigs.k8s.io/) is used for running a Kubernetes cluster locally on your own computer, and [Flux](https://toolkit.fluxcd.io/) is a tool for running [GitOps](https://www.gitops.tech/#what-is-gitops) – deploying changes to your cluster by making changes to a git repo. You will also need to have [Docker](https://docs.docker.com/docker-for-mac/install/) installed, since kind will run your Kubernetes cluster inside a Docker container (kind = **K**ubernetes **in** **D**ocker).

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

You now have a fully functional Kubernetes cluster running on your computer! Continue to [**chapter 3**](./tutorial/pod.md) to add your first pod to the cluster.

## Advanced

If you want to tear down your cluster entirely (don't worry – you can easily create a new one), you can do so with:

    kind delete cluster

If you just want to pause it to preserve CPU power on your computer, first run `docker ps` to get the container ID of your cluster. Then run `docker pause <containerId>`. To resume it later, run `docker unpause <containerId>` (who came up with that command name?!).
