---
title: "Hands-on-Kubernetes Playground"
date: 2023-03-25T22:01:35+02:00
draft: false
---

This is my story about how I started to learn Kubernetes and got some hands-on experience with it.

As I realized that just following tutorials is not going to cut it, I had to get my hands dirty.

This was **my process** of expanding my knowledge in the containers-deployment-CI/CD area and how I understood how some of the  
things work.

It also reduced the mental gap between the complex, inaccessible Kubernetes and transformed it into a command-away tool, allowing me to experiment and learn new things.

## Intro
Now that we know how to [wrap our app in a container](https://costica.dev/posts/backend-delivery-hands-on-docker-1/) and we also [leverage containers for local development](https://costica.dev/posts/backend-delivery-hands-on-docker-2/), there's only one thing missing: releasing the container into the real world.

Nowadays, it is quite an industry standard to rely on a Kubernetes cluster to run your containerized projects.

While this is overkill for small projects and containers could be used **without** Kubernetes and still get a lot of the deploy-ability benefits,
we already have that covered - just replicate the "setup" (a.k.a. install a container runtime) on a server
and make sure it is reachable by the outside world.

## What

In this article, however, we're going to set up the basics for understanding how container workloads are run in the real world -
real-life projects, that do use a container orchestrator - Kubernetes.

### Why

Because being a software engineer means not only coding but also delivering your application. To do that efficiently, one should understand
*how* the project is being run in the **prod environment**. One should know what tools he has access to, and, more importantly, what are the limitations.

#### Why this is hard

Changing your mental model from "I coded this" to a distributed systems model (Kubernetes) can be a daunting task at first: you
have to understand **tons** of different concepts and terms.

I can't speak for others, but I don't learn stuff just by reading or watching tutorials.

I need to experiment and try stuff out - experiment, fail hard, sometimes get it working, etc. But always learn something new.

This is rather hard if you're trying to do it as a deliverable for your job: permissions issues, cost inquiries, breaking stuff for your colleagues, time pressure, actually-delivering-something pressure, etc, etc.

#### The cost incident
Ok then, let's get our hands dirty in a personal project.

Not that easy, as we now need a Kubernetes cluster to play with. For me, spending money, especially in a "pay-as-you-go" setup,
like the ones that are being pushed by the major cloud providers, is a no-go.

As I don't know what I'm doing, I need to be able to try out deploying my containers and **not be afraid** of scaling
my experiment up **WITHOUT** worrying about costs.

## What I did
So I didn't want to go through complicated procedures and ask for resources from my company.

Fun fact, when I first started to look into this Kubernetes thingy, the company I worked for at the time
didn't even run their workloads using containers or K8s.

### Local Kubernetes cluster
The good news is that one can experiment and learn (possibly even more) by having a local Kubernetes cluster up & running.

⚠️ This learning path, has, like all others in this universe, some pros and cons.

#### Pros
1. No worries about the costs - since you use your own compute resources
2. You don't have anything handled out of the box for you; additional learning opportunities compared to just spinning up
   a Kubernetes cluster using a cloud provider or an online "lab"
3. No worries about breaking stuff, since you are the sole user of the cluster and can try out literally anything

#### Cons
1. Depending on your host machine OS and resources, the initial setup of the cluster can be a pain
2. You can't really experiment the fully-fledged K8s cluster, as you don't have access to all the features a
   Kubernetes cluster run by a cloud provider would have

## Prerequisites - Minikube
At the moment, I use [Minikube](https://minikube.sigs.k8s.io/docs/start/), because:
1. I got it working and was rather easy to install
2. It can be used to create a local environment to experiment and test Kubernetes resources such as pods,
   services, and deployments without the need for a full-fledged Kubernetes cluster.
3. It provides access to most of the essential features of Kubernetes, including its networking, storage, and security features.
4. It allows me to learn Kubernetes concepts such as pods, deployments, services, and more, in a safe and isolated environment.
   I can experiment with different configurations and settings.
5. It is compatible with various tools and plugins that are available in the Kubernetes ecosystem, such as Helm, Istio, and Prometheus.
6. It can spin up a multi-node cluster (most other solutions, including the Embedded Docker K8s cluster, only handle single-node clusters)

## A "real life" exercise
Now that you have a working Kubernetes cluster on your local machine, the best way to learn something is by doing something.

So here's the challenge:

* You should be able to deploy, run, and **interact** with a web server. (check out [this article](https://costica.dev/posts/backend-delivery-hands-on-docker-1/) for a tiny `node.js` web-server)
* [Bonus challenge] You should **not** expose your Docker image to the public Docker registry **at all**.

## Solving the exercise

Resources:
* [Node-quickstart server code & Dockerfile](https://github.com/costicaaa/node-quickstart/tree/v2.0-dev_and_prod_ready_quickstart)
* [Playground recipes](https://github.com/costicaaa/k8s-playground/tree/minikube-internal-registry-insecure-error)

There are quite a few things that need to happen:
1. First we need to build the image - we already know how to do that ✅
2. Push the image built at 1. to a registry (as per requirements, not the official one :)
3. Tell the K8s cluster to run the container; it should be able to use the same registry as we used for pushing at 3.
4. Test that everything is as expected - make a request to the web server running inside the cluster!

### Step 1: Building the image
Not going into details on this one, as it was already covered in a [previous article](https://costica.dev/posts/backend-delivery-hands-on-docker-1/).

Just a quick refresher, we're going to use [our simple web server](https://github.com/costicaaa/node-quickstart/tree/v2.0-dev_and_prod_ready_quickstart) and build the image like so:
```shell
docker build --no-cache --tag test_bundle
```

We now have an image called "test_bundle" that exposes [on port `7007`](https://github.com/costicaaa/node-quickstart/blob/v2.0-dev_and_prod_ready_quickstart/Dockerfile#L3).
a simple API.

### Step 2: Custom docker registry
As this is my experiment and I have no time constraints, I wanted to spice things up a bit.

Pushing an image to the default [Dockerhub](https://hub.docker.com/) is a no-brainer. The downside is that
I don't learn anything from this - companies usually host their private registries for multiple reasons.

I had a simple reason: I didn't want my code to be public and wanted to be able to experiment **fast**.
It should, at least in theory, be faster to _push_ an image to a local registry rather than over the network to the main one.

#### Local custom registry - how?
The nice thing about understanding how to run a local registry (and to make it accessible to Minikube's cluster) is that it provides a very nice learning experience:
* You need to find how to run a registry (using a container, maybe? 😉😉)
* What happens with the actual images? where are they stored when you shut down the registry? are they even persisted?
* How will Minikube be able to reach and use that registry so that it can pull the image and then run it?

Long story short, [this thing](https://hub.docker.com/_/registry) exists: it's a Docker image that is exactly what we want: a registry.

> ❗ Take a couple of minutes and give it a spin on your local machine, just so you get used to its API.
>
> Otherwise, the commands I used will be listed below in the article.

Having this registry up and running as a standalone container is nice, but I think we can do better.

What if we go Inception-style and run this container inside our own cluster? Wouldn't that be cool?

By doing this, we're basically exploring **lots** of Kubernetes features: Persistent Volumes, Deployments, Services; and also,
some Minikube intricacies: how to expose a service by using port-forwarding.

Enough talk, let's deep dive. This is what the `registry/deployment.yaml` file looks like:

```yaml
# registry/deployment.yaml 

apiVersion: v1
kind: PersistentVolume
metadata:
  name: docker-registry-pv
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 25Gi
  hostPath:
    path: /minikube/var/lib/registry
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: docker-registry-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 25Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: docker-registry
  labels:
    app: docker-registry
spec:
  replicas: 1
  selector:
    matchLabels:
      app: docker-registry
  template:
    metadata:
      labels:
        app: docker-registry
    spec:
      volumes:
        - name: docker-registry-storage
          persistentVolumeClaim:
            claimName: docker-registry-pv-claim
      containers:
        - image: registry:latest
          name: docker-registry
          ports:
            - containerPort: 5000
          volumeMounts:
            - mountPath: "/var/lib/registry"
              name: docker-registry-storage

```
* It defines a new `PersistentVolume` so that the cluster saves some storage that will be available across restarts
* It also defines a `PersistentVolumeClaim` - allowing the registry to "reserve" the persistent volume for its own use
* It defines a `Deployment` that runs the `registry:latest` *image*; it will store the images in the `PersistentVolume` we defined,
  so that we don't lose the data if we delete the `Deployment` or shut down the cluster.

With this definition setup, we need to tell the cluster to actually run it and instantiate the resources we specified:
```shell
kubectl apply -f registry/deployment.yaml
```

```shell
 persistentvolume/docker-registry-pv created
 persistentvolumeclaim/docker-registry-pv-claim created
 deployment.apps/docker-registry created
````

In a few minutes/seconds, you should see the pods up and running:
```shell
kubectl get pods 
```

```shell
 NAME                              READY   STATUS    RESTARTS   AGE
 docker-registry-b5db7866b-bxzvx   1/1     Running   0          69s
````

Now, just because we have a container running inside the cluster, that doesn't really mean anything (yet).

1. We need to be able to "interact" with the registry.

2. The registry is inside the container.

3. ❌ The container is running inside a cluster.

For 1 & 2, we get the port forwarding magic out of the box due to how the `registry:latest` image works and how we set up the `deployment`'s port forwarding.

For 3, however, we need to create a new K8s object - a `Service`:
```yaml
# registry/service.yaml

apiVersion: v1
kind: Service
metadata:
  name: docker-registry-service
  labels:
    service-name: docker-registry-service
spec:
  type: LoadBalancer
  selector:
    app: docker-registry
  ports:
    - port: 5000
      protocol: TCP
      targetPort: 5000
```

```shell
kubectl apply -f registry/service.yaml
```

>```shell
>NAME                      TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
>docker-registry-service   LoadBalancer   10.105.227.73   <pending>     5000:31298/TCP   3s
>kubernetes                ClusterIP      10.96.0.1       <none>        443/TCP          13m
>````

Now the service will be available on port `5000`, as we defined it.

The catch here is that port `5000` refers to a port inside the cluster.

⚠️ This is where the _local_ vs _cloud_ networking and access to resources start to differ. If everything we've covered so far
is K8s-only, cloud/local-agnostic, note _this_ difference. If you're doing this setup in a real-world scenario,
you would need to set up the networking between your cloud provider and the service inside the Kubernetes cluster.

The fun part: it will differ depending on your cloud provider 🤦.

For our local setup, however, Minikube is the "cloud provider". Following their [docs](https://minikube.sigs.k8s.io/docs/commands/service/), we can expose the service like so:
```shell 
minikube service docker-registry-service --url 
```
> http://192.168.64.12:31958

This will expose, on our own laptop, a "DockerHub" registry, similar to the official one. The only difference is that we need to specify it in our commands - if none is specified, the official one is used by default.

And voilá, we can now see a list of all the images that are in the registry:

```shell
curl http://192.168.64.12:31958/v2/_catalog
```

```shell
{"repositories":[]}
````

Getting closer. Now the last bit for the 2nd step (we're only at step 2, huh!). Let's push the image to the registry:

In the "node-quickstart" project, build & push the image to our private registry:

```shell
docker build . -t 192.168.64.12:31958/node-quickstart:v1
````

```shell
docker push 192.168.64.12:31958/node-quickstart:v1
````

Let's make another call to the registry API:
```shell
curl http://192.168.64.12:31958/v2/_catalog
```

```shell
{"repositories":["node-quickstart"]}
```

### Step 3: Run the container

Now that our image exists in the private registry (a registry that the cluster can access using the same service URL we use from the host machine), we can finally tell
the K8s cluster to run the container.

Luckily, we already know how to do that! 😎 😎 😎

Similar to how we deployed the `registry`, we can do the same for our service.

> Mind the `image` field, we now have to provide the full path to the image, as we're no longer using the default/official registry

```docker
apiVersion: apps/v1
kind: Deployment
metadata:
  name: node-quickstart
  labels:
    app: node-quickstart
spec:
  replicas: 1
  selector:
    matchLabels:
      app: node-quickstart
  template:
    metadata:
      labels:
        app: node-quickstart
    spec:
      containers:
        - image: 192.168.64.12:31958/node-quickstart
          name: node-quickstart
          ports:
            - containerPort: 7007
```

Apply the deployment configuration:
```shell 
kubectl apply -f node-quickstart/deployment.yaml 
```

❌ And it will fail. We can debug why using "kubectl get pods" && "kubectl describe pod POD_NAME".

We need to let Minikube use insecure registries too!

```shell 
minikube start --insecure-registry="192.168.64.12:31958"
```

After restarting the cluster, it still won't work. For Minikube to allow `--insecure-registry` to work, we need to first delete the cluster and then restart it.


🥚 / 🐣 / 🐓 ?

We're now stuck in a chicken-and-egg kind-of dilemma: we can't get the URL of the insecure registry before we start the cluster, and we need it before starting the cluster the first time.

## What now?

Resources:
* [Node-quickstart server code & Dockerfile](https://github.com/costicaaa/node-quickstart/tree/v2.0-dev_and_prod_ready_quickstart)
* [Playground recipes](https://github.com/costicaaa/k8s-playground/tree/node-quickstart-in-minikube/node_quickstart_in_minikube)

We could give up and just use the official Docker repository. Nothing wrong with that.

Let's not give up just yet. Going through Minikube's docs some more, it seems like we might not need a repository at all 🤯.

Since we're already building the image locally, we could just "load" it inside the cluster spun up by Minikube by using the `minikube load image IMG_NAME` command.

The only caveat: we need to change the `imagePullPolicy` to `Never` in the `deployment.yaml` file such that the Kubernetes cluster will resolve to the internal one we just loaded.

We can then reference the image without the registry option: `image: node-quickstart`.

Putting it all together, it seems we spent a lot of time "not doing anything". We can complete the exercise with only these simple steps:

1. Build the image (`node-quickstart` folder): `docker build -t node-quickstart .`
2. Start a brand-new cluster: `minikube -p easy-mode start`
3. Load the image: `minikube image load node-quickstart:latest`
4. Start the deployment of the container: `kubectl apply -f deployment.yaml`
5. Start the service: `kubectl apply -f service.yaml`
6. Expose the service to the outside world: `minikube -p easy-mode service node-quickstart-service --url`
7. Copy and paste the `url` returned by the previous command. `Hello world` is returned by the
* app running inside
* a container running inside
* a pod running inside
* the cluster running inside a
* VM running inside
* your local machine.

## What the heck was that all about?
It was a learning experience. While I managed to complete what I wanted, but not **how** I wanted - I gained so much more from it.

I learned how **not to do it**. Maybe I missed some things, maybe it is possible and I was just one command or setting away
from making it work. And that's alright.

For me, however, the greatest value is having a deeper understanding of how various things work.

## What next?
I am, obviously, not happy with the final outcome. I learned something is impossible (or at least hard to set up locally) the way I wanted.

Multiple options ahead:

1. Keep the registry "isolated" - as an "external" service to the k8s cluster.
2. Find a way to enable TLS on the registry inside the cluster, so that the `kubectl` commands can pull images.
3. Try out other "micro k8s" distributions that I could run locally. Maybe some of those don't have the chicken-or-egg existential issues :)

This is getting too long, however. I will stay true to the initial theme of the blog: documenting experiments.

This one was but **one "failure"** in terms of achieving what I wanted.

But a **complete success** in terms of learning how things work. 10/10 would recommend it.

Until next time, by-bye! 