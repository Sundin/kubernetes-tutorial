# Chapter 4: Add an ingress to your pod

By using the port-forward solution described at the end of [chapter 3](./pod.md), we will only get direct access to one of the two pods. A more robust solution is to use an [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) in front of our service, but this will require some more work. Step one is to set up a brand new cluster with an [Ingress Controller] installed.

## Recreate cluster

Delete the existing cluster:

    kind delete cluster

Create a kind cluster with `extraPortMappings` to allow the local host to make requests to the Ingress controller over ports 80/443 and `node-labels` to only allow the ingress controller to run on a specific node(s) matching the label selector. These settings have been preconfigured in the [kind/cluster-config.yaml](./kind/cluster-config.yaml) file.

    kind create cluster --config=kind/cluster-config.yaml

Add an [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/deploy/) to the cluster:

    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml

## Synch Flux to new cluster

Tell Flux to start synching to your new cluster instead. This is the same as the initial bootstrapping command we used in [chapter 3](./tutorial/pod.md), but without creating a new repository. If you prefer, you can instead create a new Flux repository instead by using the `bootstrap` command described in [chapter 3](./tutorial/pod.md).

    flux install

    flux create source git flux-system \
        --url=https://github.com/$GITHUB_USER/kubernetes-tutorial \
        --branch=master \
        --interval=1m

    flux create kustomization flux-system \
        --source=flux-system \
        --path="./clusters/my-cluster" \
        --prune=true \
        --interval=10m

After Flux have successfully reconciliated (synched) the changes yo your cluster, your service is now available at http://localhost:80/swagger/index.html.

Or:

    curl -X GET "http://localhost:80/WeatherForecast" -H  "accept: text/plain"

If you have any problems, you might need to look in to the logs from the NGINX ingress controller pod itself. It runs in a different _Kubernetes namespace_ (list all namespaces with `kubectl get namespaces`) so you have to use the `-n` flag like this:

    stern -n ingress-nginx "ingress-nginx-.*"

## How it works

In the `kustomize` folder of the weather-api repo, there's a file called `ingress.yaml` with the following content:

    apiVersion: networking.k8s.io/v1beta1
    kind: Ingress
    metadata:
        name: example-ingress
    spec:
    rules:
        - http:
            paths:
            - path: /
                backend:
                serviceName: weather-api
                servicePort: 5000

Previously, it had no effect. But now when we have the NGINX ingress controller deployed, NGINX will use the `/` path rule to route all incoming traffic to the weather-api running on port 5000. As we add more and more services to the cluster, we can create more fine-grained path rules in order to route traffic correctly.

## Advanced

Since Kind eats up quite some CPU resources, it might make sense in pausing it when you don't need it. To pause the cluster, first run `docker ps` to get the container ID of your cluster. Then run `docker pause <containerId>`. To resume it, run `docker unpause <containerId>` (who came up with that command name?!).
