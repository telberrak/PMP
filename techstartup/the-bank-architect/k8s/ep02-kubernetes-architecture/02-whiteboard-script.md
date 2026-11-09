**Kubernetes from Zero, Episode 2: Kubernetes Architecture, the Control Plane and the Nodes**
(About 11 to 12 minutes with the demo. Format: Detailed, timed at 228 spoken words per minute plus pauses.)
(Voice: conversational-speech-script (no running analogy), adapted with teleprompter-script-adapter.)
(Source: kubernetes.io/docs, "Cluster Architecture", "Kubernetes Components", "Communication between Nodes and the Control Plane" and "Controllers" pages.)
Bold = stress. / = brief pause. [pause] = 1 second. [long pause] = 2 to 3 seconds.

=== PART 1. COLD OPEN ===

[ON SCREEN: what happens after kubectl apply]

{intro} You type one command, / and a container starts on a machine you never touched. [pause]

That command is kubectl apply. / A few seconds later, / there's a running container, / somewhere in your cluster.

But in between, / there's a black box. [pause]

Who decided **which** machine? / Who pulled the image? / And who remembers that this app should exist at all?

This is episode two of Kubernetes from Zero, / and today we open that black box.

Let me give you the short answer first. [pause]

{key} The control plane decides, / the nodes do the work, / and everything talks through the API server.

First, / we'll look at the big picture. / Then we'll meet each component, / one at a time. / And finally, / we'll follow one request, / from your keyboard to a running container.

So, / let's start with the big picture. [long pause]

=== PART 2. THE BIG PICTURE ===

[ON SCREEN: a cluster is a control plane plus nodes]

{def} A Kubernetes **cluster** has two halves: / a brain, / and the muscle.

The first half is the control plane. / That's the brain.

It makes the big decisions, / like where things run. / And it notices and reacts to events, / like a crashed container.

The second half is the worker nodes. / That's the muscle.

{def} A node is a machine, / physical or virtual, / that actually **runs** your containers.

Your containers live inside pods, / and pods only ever run on nodes. [pause]

Every cluster needs at least one worker node. / In production, / you'll usually have many.

And the control plane normally runs on its own machines, / so a busy app can't starve the brain.

Here's something a lot of people don't know. / On managed services like GKE, / EKS, / or AKS, / the cloud provider runs the control plane for you.

You never see those machines. / But they're still there, / doing exactly the same job.

So, / let's look inside the brain first. [long pause]

=== PART 3. THE CONTROL PLANE ===

[ON SCREEN: the control plane components]

{tr} The control plane isn't one program. / It's a small team of programs.

There's the API server.

There's a database, / called etcd.

There's the scheduler.

There's the controller manager.

And there's one more that's optional: / the cloud controller manager. [pause]

So, / five pieces. / Let's meet them one at a time, / starting with the front door. [long pause]

=== PART 4. THE API SERVER ===

[ON SCREEN: the API server is the front door]

{def} The **API server** is the front door of the cluster, / and every request goes through it.

No exceptions.

When you run kubectl, / it's really just sending web requests to the API server.

And here's a nice trick. / Add dash v equals six to any kubectl command, / and you'll see those requests for yourself.

The controllers talk to the API server. / The agent on every node, / the kubelet, / talks to the API server too.

And the API server is the **only** component that talks to the database, / etcd. [pause]

We call this a **hub and spoke** design. / The API server is the hub, / and everything else is a spoke.

Now, / before the API server accepts a request, / it checks three things.

Who are you? / Are you allowed to do this? / And does this object make sense?

In other words, / authenticate, / authorize, / and validate. [pause]

Only then is the change saved. / And that brings us to where it's saved. [long pause]

=== PART 5. ETCD ===

[ON SCREEN: etcd is the cluster's memory]

{tr} Everything is saved in etcd, / and etcd is the cluster's memory.

{def} In simple terms, / it's a reliable database that stores keys and values. / The docs call it a consistent, / highly available key-value store.

Every object in your cluster lives here. / Every deployment, / every pod, / every secret.

{key} That makes etcd the cluster's single **source of truth**.

If it's not in etcd, / as far as Kubernetes is concerned, / it doesn't exist.

In production, / etcd runs as a small group, / usually three or five members.

It's an odd number, / so the members can always agree on a majority. / With three, / you can lose one and keep going.

And because everything lives here, / crucially, / you need backups. [pause]

Lose etcd without a backup, / and you've lost the cluster's memory. / We'll do a real backup and restore later in the series, / in episode forty-four.

So, / the change is saved. / Now, / who decides where it runs? [long pause]

=== PART 6. THE SCHEDULER ===

[ON SCREEN: the scheduler picks a node]

{tr} The **scheduler** decides which node runs each pod.

It watches for one thing: / an unscheduled pod. / That's a pod that exists, / but has no node yet.

For each one, / it runs two steps.

First, / filter. / It throws out every node that can't run the pod. / Not enough CPU? / Not enough memory? / Out.

Second, / score. / It ranks the nodes that are left. / Which one has the most room? / Which one spreads the app out best?

And if **no** node passes the filter, / the pod simply waits, / in Pending, / until one does.

Finally, / it picks the winner, / and does the bind. [pause]

Bind just means writing the node's name into the pod.

{key} So the scheduler **decides** where a pod runs. / But it doesn't start anything.

Which raises a fair question: / who makes sure things actually happen? [long pause]

=== PART 7. THE CONTROLLER MANAGER ===

[ON SCREEN: the controller manager runs control loops]

{tr} The **controller manager** is where the control loops live.

Remember episode one? / Observe, / compare, / act? / This is the home of those loops.

Each loop is called a controller, / and each one looks after one kind of object.

For example, / the Node controller notices when a node stops answering.

The Job controller creates pods to run a task to the end.

The EndpointSlice controller keeps track of which pods sit behind each Service.

And the ServiceAccount controller creates a default account in every new namespace. [pause]

There are many more. / Deployments and ReplicaSets have their own controllers too.

{key} Here's the neat part. / Each controller is separate in theory, / but they all run together as **one** program, / to keep things simple.

There's one more piece in the brain, / and it only matters in the cloud. [long pause]

=== PART 8. THE CLOUD CONTROLLER MANAGER ===

[ON SCREEN: the cloud controller manager talks to your cloud]

{tr} The cloud controller manager connects your cluster to your cloud provider.

It talks to the cloud provider's API, / and keeps cloud-specific code out of the core of Kubernetes.

It has three main jobs.

First, / nodes. / If a virtual machine is deleted in the cloud, / it removes that node from the cluster.

Second, / routes. / It sets up network routes inside the cloud.

And third, / load balancers. / When you ask for one, / it creates a real cloud load balancer for you. [pause]

On a local cluster on your laptop, / you won't have one at all. / And honestly, / that's fine.

So that's the brain. / Now, / let's look at the muscle. [long pause]

=== PART 9. THE WORKER NODE ===

[ON SCREEN: inside a worker node]

{tr} Every worker node runs three programs, / and then your pods.

First, / the **kubelet**.

{def} The kubelet is an agent on every node. / It makes sure the containers in each pod are running, / and healthy.

It also restarts containers that fail their health checks. / And it sends a regular heartbeat to the API server, / so the brain knows the node is alive.

Second, / the container runtime. / As we saw last time, / that's usually containerd or CRI-O. / The kubelet tells it what to run.

Third, / a network helper called kube-proxy. / It keeps network rules on the node, / so traffic sent to a Service reaches the right pods.

Some network plugins replace it, / so on some clusters you won't see it. [pause]

{key} And then, / of course, / the reason the node exists at all: / your pods.

There are just a few more pieces that make a cluster complete. [long pause]

=== PART 10. ADD-ONS ===

[ON SCREEN: add-ons complete the cluster]

{tr} On top of the core, / clusters run add-ons.

The most important one is cluster DNS. / It lets pods find each other by name, / and it's usually a program called CoreDNS.

Next, / you need a network plugin. / It gives every pod its own IP address, / and it follows a standard called CNI. / We'll spend all of episode fifteen on it.

And then there are optional extras: / metrics, / a web dashboard, / and logging. [pause]

Right. / We've met every piece. / So now, / let's watch them work together. [long pause]

=== PART 11. LIFE OF A DEPLOYMENT, PART ONE ===

[ON SCREEN: life of a deployment, part one]

{key} Let's follow one request, / from your keyboard to a running container.

You run kubectl apply, / with a deployment that asks for three copies.

Step one. / kubectl sends it to the API server.

The API server checks who you are, / checks your permissions, / validates the object, / and stores it in etcd. [pause]

Step two. / The deployment controller is watching. / It sees the new deployment, / and creates something called a ReplicaSet.

Step three. / The ReplicaSet controller sees that, / and creates three pods.

But here's the thing. / Those three pods have **no node** yet. / They're pending.

So, / who picks them up? / Let's find out. [long pause]

=== PART 12. LIFE OF A DEPLOYMENT, PART TWO ===

[ON SCREEN: life of a deployment, part two]

{tr} Step four. / The scheduler spots three unscheduled pods. / It filters, / scores, / and binds each one to a node.

Step five. / On each chosen node, / the kubelet is watching. / It sees a pod for its own node, / and says, / that one's mine.

Step six. / The kubelet tells the container runtime to pull the image, / and start the containers.

Step seven. / The kubelet reports back: / running. [pause]

{key} Now, / notice something. / Nobody called anybody directly. / Every component just **watches** the API server, / and reacts to what changed.

Here's a question people often get in interviews. [pause]

What happens if the whole control plane goes down?

Your running pods keep running. / The kubelet doesn't need the brain to keep containers alive.

But nothing **new** can happen. / No scheduling, / no scaling, / no self-healing, / until the control plane is back.

{key} That's why production clusters run the control plane on three or more machines.

Enough theory. / Let's look at a real cluster. [long pause]

=== PART 13. DEMO: LOOK INSIDE A REAL CLUSTER ===

[ON SCREEN: demo, look inside a real cluster]

(DEMO: local kind cluster. kubectl get nodes -o wide; kubectl get pods -n kube-system; kubectl create deployment web --image=nginx --replicas=3; kubectl get events --sort-by=.lastTimestamp)

{tr} This is a small local cluster. / We'll build it together next episode.

First, / kubectl get nodes. / Here's my node, / with its role, / its Kubernetes version, / and its container runtime. [pause]

Now, / kubectl get pods, / in the kube-system namespace.

Look at this. / There's etcd. / There's the API server. / There's the controller manager, / and the scheduler.

On this kind of cluster, / the control plane itself runs as pods. / So it's Kubernetes, / running Kubernetes.

And here's kube-proxy, / and two CoreDNS pods. [pause]

Now, / let's create a deployment with three replicas, / and look at the events, / with kubectl get events.

Let's read them top to bottom. / The deployment controller scaled up a ReplicaSet. / The ReplicaSet controller created the pods.

The scheduler assigned each pod to a node. / Then the kubelet: / pulling the image, / created, / started.

{key} That's the whole life story, / told by the cluster's own events.

Let's recap, / and I'll use one quick comparison to make it stick. [long pause]

=== PART 14. RECAP ===

[ON SCREEN: recap, the cluster as a restaurant]

{tr} Picture a restaurant, / just for a moment.

The API server is the front desk. / Every order goes through it.

etcd is the order book. / If it's not written there, / it didn't happen.

The scheduler is the host, / deciding which station gets each order.

The controllers are the managers, / always checking that what was ordered is what's being served.

And the kubelet is the cook at each station, / actually making the food. [pause]

{key} And that's the short answer we started with. / The control plane decides, / the nodes do the work, / and everything talks through the API server. [long pause]

=== PART 15. WHAT'S NEXT ===

[ON SCREEN: next, set up a local cluster]

Which component surprised you most? / Let me know below.

And subscribe if you'd like to build along with me.

So far, / you've watched me use a cluster. / Next time, / you'll build your own, / on your laptop, / for free.

We'll compare three tools: / kind, / minikube, / and k3d. / And I'll tell you which one I picked for this series, / and why.

See you there. [long pause]
