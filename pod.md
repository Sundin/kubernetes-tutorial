# Chapter 3: Launching your first pod

## Create Flux repo

Run the following command to create a repository that will contain the configuration for your Kubernetes cluster:

    flux bootstrap github \
        --owner=$GITHUB_USER \
        --repository=kubernetes-tutorial \
        --branch=master \
        --path=./clusters/my-cluster \
        --personal

Clone the repo the newly created repository.

    git clone git@github.com:$GITHUB_USER/kubernetes-tutorial.git

## Deploy pod

We will now deploy a pod called [weather-api](https://github.com/Sundin/weather-api), which is a tiny sample web application made with .NET Core 5.0, to our cluster. In order to do this, we will create a _GitRepository manifest_ (a file that defines that we will use a git repository as source for our pod) pointing to the weather-api repository's master branch. Other example of source types than git are [Helm](https://helm.sh/) repositories or file buckets.

    flux create source git weather-api \
        --url=https://github.com/Sundin/weather-api \
        --branch=master \
        --interval=30s \
        --export > ./clusters/my-cluster/weather-api-source.yaml

We will also create a _Flux Kustomization manifest_ for the weather-api pod. The Kustomization API defines a pipeline for fetching, decrypting, building, validating and applying Kubernetes manifests, so this will configure Flux to build and apply the [kustomize directory](https://github.com/Sundin/weather-api/tree/master/kustomize) located in the weather-api repository.

    flux create kustomization weather-api \
        --source=weather-api \
        --path="./kustomize" \
        --prune=true \
        --validation=client \
        --interval=5m \
        --export > ./clusters/my-cluster/weather-api-kustomization.yaml

Commit and push the changes.

Watch Flux sync the application (this process is technically known as _[reconciliation](https://toolkit.fluxcd.io/core-concepts/#reconciliation)_):

    watch flux get kustomizations

When the synchronization finishes you can check that weather-api has been deployed on your cluster:

    watch kubectl -n default get deployments,services

_(Note: if you don't have watch installed, you can run the same commands without prefixing watch.)_

From this moment forward, any changes made to the weather-api Kubernetes manifest in the master branch will be synchronised with your cluster.

To follow logs in realtime for your pods, run:

    stern "weather-api-.*"

Alternatively, you can run `kubectl get pods` to figure out the name of one of your pods and then do `kubectl logs ${POD_NAME} ${CONTAINER_NAME}`.

## Port forwarding

At this point you might want to try out the actual service we now have up and running. A quick and easy way to do this when developing locally is simply to tell Kubernetes that we want to open the port where our service is running.

First of all we need to find out the name of some pod which we then can access. List all currently running pods with:

    kubectl get pods

Grab the name of any of the running weather-api pods (there should be two of them) and run the following command to forward port 8080 on your computer to port 9898 of the pod, which is were weather-api is listening for incoming traffic.

    kubectl port-forward <podname> 8080:5000

Access the weather-api Swagger API documentation at http://localhost:8080/swagger/index.html

Or use `curl` to play around with the API itself:

    curl -X GET "http://localhost:8080/WeatherForecast" -H  "accept: text/plain"

## Advanced

If a Kubernetes manifest is removed from the weather-api repository, Flux will remove it from your cluster. If you delete a Kustomization from the kubernetes-tutorial repository, Flux will remove all Kubernetes objects that were previously applied from that Kustomization.

If you alter the weather-api deployment using `kubectl edit`, the changes will be reverted to match the state described in Git. When dealing with an incident, you can pause the reconciliation of a kustomization with `flux suspend kustomization <name>`. Once the debugging session is over, you can re-enable the reconciliation with `flux resume kustomization <name>`.

Since Kind eats up quite some CPU resources, it might make sense in pausing it when you don't need it. To pause the cluster, first run `docker ps` to get the container ID of your cluster. Then run `docker pause <containerId>`. To resume, run `docker unpause <containerId>` (who came up with that command name?!).
