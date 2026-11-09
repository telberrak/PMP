**Kubernetes from Zero, Episode 3: Set Up a Local Cluster with kind, minikube and k3d**
(About 11 to 12 minutes with the demo. Format: Detailed, timed at 228 spoken words per minute plus pauses.)
(Voice: conversational-speech-script (no running analogy), adapted with teleprompter-script-adapter.)
(Source: kubernetes.io/docs "Install Tools" and "Organizing Cluster Access Using kubeconfig Files"; kind.sigs.k8s.io; minikube.sigs.k8s.io; k3d.io.)
Bold = stress. / = brief pause. [pause] = 1 second. [long pause] = 2 to 3 seconds.

=== PART 1. COLD OPEN ===

[ON SCREEN: a Kubernetes cluster on your laptop]

{intro} A three-node Kubernetes cluster, / on your laptop, / in under a minute. [pause]

That's what we're building today.

Not in the cloud. / No credit card. / And no surprise bill at the end of the month.

Just your laptop, / and a real Kubernetes cluster running on it.

Welcome to episode three.

In the last two episodes, / you watched me use a cluster. / From today, / you'll have your own, / and you can follow every demo in this series.

The whole setup comes down to three roles. [pause]

{key} Docker runs the containers, / a cluster tool builds the cluster, / and kubectl talks to it.

First, / what you need. / Then how kubectl finds a cluster. / Then three tools, / and which one I picked. / And finally, / we build one together.

So, / let's start with your shopping list. [long pause]

=== PART 2. WHAT YOU NEED ===

[ON SCREEN: what you need]

{tr} You need just three things, / and they're all free.

First, / Docker, / or Podman.

All three tools we'll look at today run Kubernetes inside containers. / So you need something that runs containers. / Docker Desktop and Podman both work.

My advice: / give Docker room to breathe. / Four CPUs and eight gigabytes of memory is a comfortable starting point.

And if you're on Windows, / turn on WSL two. / Docker Desktop uses it under the hood, / and everything runs much faster.

Second, / kubectl. / This is the command-line tool you'll use to talk to every Kubernetes cluster, / local or in the cloud.

And third, / a cluster tool. / Something that builds the cluster for you. [pause]

{key} So, / Docker runs the containers. / The cluster tool builds the cluster. / And kubectl talks to it.

But before we build anything, / there's one question worth answering. / How does kubectl know where your cluster is? [long pause]

=== PART 3. KUBECTL AND KUBECONFIG ===

[ON SCREEN: how kubectl finds your cluster]

{tr} kubectl finds your cluster through one small file.

It starts with you, / typing a command.

Now, / kubectl doesn't know where your cluster lives. / So it reads that file first.

{def} The file is called the kubeconfig. / It's a small YAML file, / usually in your home folder, / under dot kube, / slash config.

Inside, / there are three lists. / Clusters, / which are addresses. / Users, / which are credentials. / And contexts, / which pair one cluster with one user.

And one line, / called current-context, / says which context kubectl uses right now.

So kubectl reads that, / and then sends your request to the cluster's API server. [pause]

Here's the good news. / Every tool in this video writes that file for you.

One version rule worth knowing. / The docs say kubectl should stay within **one minor version** of your cluster.

So a version one thirty-four kubectl is fine with a one thirty-three, / one thirty-four, / or one thirty-five cluster.

Right. / Now, / let's pick the tool that builds the cluster. [long pause]

=== PART 4. THREE LOCAL OPTIONS ===

[ON SCREEN: three local options]

{tr} There are three popular tools for running Kubernetes on your laptop, / and they all work.

The first is kind.

The second is minikube.

And the third is k3d.

You might also have seen Kubernetes built into Docker Desktop, / or Rancher Desktop. / Those work too. / But these three are the most common, / and honestly, / the most flexible.

They all give you a working cluster, / but they make different trade-offs.

So, / let's look at each one, / starting with the one I use. [long pause]

=== PART 5. KIND ===

[ON SCREEN: kind, Kubernetes in Docker]

{def} **kind** stands for Kubernetes IN Docker, / and that's exactly what it does.

Remember the nodes from last episode? / Normally, / a node is a machine.

With kind, / each node is just a Docker container.

So when I build a three-node cluster, / I get one container called kind-control-plane, / and two more called kind-worker, / and kind-worker2.

Inside each one runs a real kubelet, / and a real container runtime. / So it's genuine, / upstream Kubernetes.

Fun fact. / kind was built to test Kubernetes itself. / The Kubernetes project uses it in its own test pipelines.

That makes it fast to create, / fast to delete, / and very close to the real thing. [pause]

There's one small catch. / On your laptop, / there's no cloud to hand out load balancers.

So kind has a small helper project for that, / called cloud-provider-kind. / We'll use it when we get to services.

Next up, / the oldest of the three. [long pause]

=== PART 6. MINIKUBE ===

[ON SCREEN: minikube, batteries included]

{tr} **minikube** is the batteries-included option.

It's the oldest of the three, / and it's maintained by the Kubernetes project too.

By default, / it gives you a single node.

It can run that node in different ways, / and minikube calls them drivers. / The Docker driver runs it as a container. / Other drivers run it in a full virtual machine.

Now, / minikube's superpower is addons. / With one command, / you can turn on extras, / like an ingress controller, / or the metrics server.

And there's a built-in web dashboard. / Type minikube dashboard, / and it opens in your browser. [pause]

The everyday commands are simple: / minikube start, / minikube stop, / and minikube delete.

{key} If you like a guided, / batteries-included experience, / minikube is a great pick.

And now, / the lightest of the three. [long pause]

=== PART 7. K3D ===

[ON SCREEN: k3d, k3s in Docker]

{tr} **k3d** gives you the fastest, / lightest cluster.

It runs something called k3s inside Docker.

{def} In simple terms, / k3s is a small version of Kubernetes. / It's packaged as a single binary, / and it's popular on edge devices and small servers.

Because it's so lightweight, / k3d clusters start very fast, / and use little memory.

k3s also ships with a few things built in. / Traefik as the ingress controller, / and a provisioner for local-path storage. [pause]

That's convenient. / But it also makes it a little different from a plain upstream cluster. / So watch for that when you follow tutorials.

So, / three good tools. / Which one should you actually pick? [long pause]

=== PART 8. WHICH ONE SHOULD YOU PICK ===

[ON SCREEN: which one should you pick]

{tr} My short answer: / it depends on what you want to learn.

If you want to learn upstream Kubernetes, / and easily build multi-node clusters, / pick kind.

If you want addons, / a dashboard, / and a guided experience, / pick minikube.

If you want the fastest and lightest cluster possible, / pick k3d. [pause]

{key} For this series, / I'm using **kind**.

It's close to upstream, / a multi-node cluster takes one small file, / and it's what you've already seen in my demos.

But please, / don't feel locked in. / Everything we do will work on minikube and k3d too.

So, / let's install it. [long pause]

=== PART 9. INSTALL ===

[ON SCREEN: install kubectl and kind]

{tr} Installing takes two commands on most machines.

On macOS, / the easiest route is Homebrew. / brew install kubectl, / and brew install kind.

On Windows, / use winget, / or Chocolatey. / Both have packages for kubectl and kind.

On Linux, / you download a single binary for each tool, / make it executable, / and move it onto your path. [pause]

I'll put every install link in the description.

When you're done, / check that it worked. / Run kubectl version, / with dash dash client. / And then, / kind version.

Everything installed? / Then let's build a cluster. [long pause]

=== PART 10. DEMO: CREATE A MULTI-NODE CLUSTER ===

[ON SCREEN: demo, create a three-node cluster]

(DEMO: create kind-config.yaml with one control-plane and two worker nodes. kind create cluster --name k8s-zero --config kind-config.yaml. kubectl get nodes. docker ps)

{tr} First, / a small config file. / I'll call it kind-config.yaml.

It says: / kind, / cluster. / And then a list of nodes.

One node with the role control plane. / And two with the role worker. / That's the whole file.

Now, / kind create cluster, / with the name k8s-zero, / and dash dash config, / pointing at that file.

kind pulls a node image, / starts three containers, / and joins them into one cluster. [pause]

Under the hood, / kind uses kubeadm, / which is the official tool for setting up clusters.

Done. / And notice the last line. / kind has already added a context to our kubeconfig, / and made it the current one.

Now, / kubectl get nodes. / Three nodes. / One control plane, / two workers. / All Ready. [pause]

And here's the fun part. / Let's run docker ps.

There they are. / Our three nodes, / as three Docker containers.

So, / we have a cluster. / Let's put something on it. [long pause]

=== PART 11. DEMO: YOUR FIRST APP ===

[ON SCREEN: demo, your first app]

(DEMO: kubectl create deployment hello --image=nginx --replicas=2; kubectl get pods -o wide; kubectl expose deployment hello --port=80; kubectl port-forward service/hello 8080:80; open http://localhost:8080)

{tr} I'll run kubectl create deployment hello, / with the nginx image, / and two replicas.

That creates a deployment, / and the deployment creates the pods.

Now, / kubectl get pods, / dash o wide.

Two pods, / both running. / And look at the node column. / The scheduler spread them across both workers.

Next, / kubectl expose deployment hello, / on port eighty.

That gives our pods one stable front door, / and we call that a service. / We'll go deep on services in episode sixteen.

Finally, / kubectl port-forward, / to that service, / from port eighty eighty on my laptop.

Now, / open your browser, / and go to localhost colon eighty eighty. [pause]

Welcome to nginx. / Served from **your** own cluster. [pause]

Press control C to stop the port-forward. / The app keeps running. / Only the tunnel to your laptop closes.

Now, / one more thing you'll use every single day. [long pause]

=== PART 12. CONTEXTS: SWITCHING CLUSTERS ===

[ON SCREEN: switching between clusters with contexts]

{tr} Switching clusters takes one command.

Run kubectl config get-contexts. / It lists every context in your kubeconfig.

Each tool adds its own, / with a name that tells you who made it.

kind gives you a context like kind-k8s-zero.

minikube names its context just minikube.

And k3d adds a k3d prefix, / like k3d-dev. [pause]

To switch, / run kubectl config use-context, / and the name.

Many people also install a small helper called kubectx, / or show the current context in their terminal prompt.

{key} Here's my honest advice. / Always check your context before you run a command. / Deleting something in the wrong cluster is a classic, / painful mistake.

Let's finish with how to clean up. [long pause]

=== PART 13. CLEAN UP AND TIPS ===

[ON SCREEN: clean up and tips]

{tr} When you're done for the day, / clean up with one command.

kind delete cluster, / with the name k8s-zero. / It's gone in seconds, / and you can rebuild it just as fast. [pause]

Two quick tips.

First, / watch your Docker resources. / If pods get stuck in Pending, / Docker may simply be out of memory.

Second, / kind can't see images you built on your laptop by itself. / Use kind load docker-image, / to copy your local image into the cluster.

So, / let's wrap up what we did today. [long pause]

=== PART 14. RECAP ===

[ON SCREEN: recap]

{tr} So, / what did we cover?

{key} You need Docker, / plus kubectl, / plus a cluster tool.

{key} The three tools are kind, / minikube, / and k3d. / kind runs nodes as containers, / minikube brings addons, / and k3d runs lightweight k3s.

{key} And kubectl finds your clusters through the kubeconfig, / with one context per cluster. [pause]

Three roles, / then. / Docker runs the containers, / a cluster tool builds the cluster, / and kubectl talks to it.

And you now have a real, / three-node Kubernetes cluster, / on your own laptop. [long pause]

=== PART 15. WHAT'S NEXT ===

[ON SCREEN: next, kubectl and YAML]

Which tool are you using: / kind, / minikube, / or k3d? / And why? / I read the comments.

The next episodes all build on this cluster, / so subscribe if you don't want to miss them.

So far, / I've been typing commands, / and you've been copying them. / Next time, / we slow down, / and really learn kubectl.

The commands you'll use every day, / and how to read and write a YAML manifest, / line by line.

See you there. [long pause]
