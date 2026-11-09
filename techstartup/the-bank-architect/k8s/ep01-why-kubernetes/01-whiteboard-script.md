**Kubernetes from Zero, Episode 1: Why Containers, and Why Kubernetes After That**
(About 11 to 12 minutes with the two demos. Format: Detailed, timed at 228 spoken words per minute plus pauses.)
(Voice: conversational-speech-script (no running analogy), adapted with teleprompter-script-adapter.)
(Source: kubernetes.io/docs, "Overview" and "Containers" concept pages.)
Bold = stress. / = brief pause. [pause] = 1 second. [long pause] = 2 to 3 seconds.

=== PART 1. COLD OPEN ===

[ON SCREEN: it works on my machine]

{intro} It works on my machine. [pause]

Most of us have said it at least once. / And if you work in operations, / you've heard it far too often.

Here's what it usually looks like. / On the developer laptop, / the app runs, / the tests pass, / everything is green.

Then we ship it to the production server, / and it **falls over**. [pause]

Maybe it's a different library version. / Maybe it's a missing config file. / Maybe it's a slightly different operating system.

Here's the thing. / That one sentence is the reason **containers** exist. [pause]

And containers are the reason **Kubernetes** exists.

If you're new here, / hi, / and welcome. / I'm a solution architect, / and this is episode one of Kubernetes from Zero.

Here's the big idea for today. [pause]

{key} Containers make your app run the same everywhere, / and Kubernetes keeps all those containers running the way you asked. [pause]

We'll go from physical servers, / to virtual machines, / to containers, / and finally to Kubernetes, / including what it **doesn't** do.

So, / let's go back in time, / to where it all started. [long pause]

=== PART 2. THE TRADITIONAL DEPLOYMENT ERA ===

[ON SCREEN: traditional deployment]

{tr} The short version is this: / in the early days, / every app shared one machine, / and nothing kept them apart.

The docs call this the **traditional deployment** era.

At the bottom, / the hardware. / On top of it, / one operating system. / And on top of that, / every app the company needed. / Billing, / search, / reports, / all on the same machine. [pause]

So here's the problem. / There was no way to draw **resource boundaries** between those apps.

If billing suddenly ate all the memory, / search and reports slowed down with it.

The usual fix was to give every app its own physical server.

That works, / but most of those servers sat **almost idle**, / and each one cost real money to run. [pause]

So the industry went looking for a better way, / and that brings us to virtual machines. [long pause]

=== PART 3. VIRTUAL MACHINES ===

[ON SCREEN: virtualized deployment]

{tr} Virtual machines fixed the isolation problem, / but they made everything heavier.

Same hardware at the bottom, / but now there's a new layer on top: / the hypervisor.

In simple terms, / the hypervisor lets one physical machine pretend to be several computers. / We call each one a **virtual machine**, / or VM.

Each VM is a full computer in software, / with its own guest operating system.

So billing lives in one VM, / search in another, / and reports in a third.

{key} Now the apps are **isolated**. / One can't steal another's memory, / or read another's data.

But look at what each VM carries. / A **full operating system**, / every single time.

That means gigabytes of disk, / minutes to boot, / and patches and licences for every copy.

So we isolated our apps, / but we paid for it with a lot of extra **weight**. [pause]

Which brings me to the idea that changed everything: / the container. [long pause]

=== PART 4. CONTAINERS ===

[ON SCREEN: container deployment]

{tr} Containers keep the isolation, / but drop the weight.

This is the **container deployment** era. / Same hardware.

But this time, / there's just **one** operating system, / and every container shares the host OS kernel.

On top of it sits a program that starts and stops containers. / We call that a container runtime, / and two common ones are containerd and CRI-O.

Kubernetes plugs into them through a standard called the Container Runtime Interface, / or CRI. / And images you build with Docker run on all of them.

And each app, / billing, / search, / and reports, / now runs in its own container.

{def} Containers are isolated like VMs, / but they **share** the host operating system instead of bringing their own. / That's what makes them so **lightweight**.

A container starts in seconds, / sometimes in less than one. / An image is often tens of megabytes, / not gigabytes.

Same image format, / same tools, / and you can pack hundreds of them onto one machine. [pause]

{key} A VM virtualizes the **hardware**. / A container virtualizes the **operating system**.

So, / let's open one of those boxes, / and see what's really inside. [long pause]

=== PART 5. WHAT A CONTAINER REALLY IS ===

[ON SCREEN: what is a container]

{tr} Here's the surprise: / a container is just a normal program, / with a fence around it.

It begins with a recipe, / usually a file called a Dockerfile.

You build that recipe into a container **image**.

{def} The image is a ready-to-run package: / your code, / the runtime it needs, / its libraries, / and its default settings.

And crucially, / it's **immutable**. / Once it's built, / it doesn't change.

Then you push the image to a shared library of images. / We call that an image registry, / like Docker Hub, / or your company's private one.

Any machine can pull it from there, / and start a running container. / Same image, / same behaviour, / on a laptop or in production. [pause]

Now, / under the hood, / there's no magic. / A container is a normal Linux process, / with two kernel features wrapped around it.

The first one controls what the process can **see**: / its own files, / its own network, / its own process list. / Linux calls these namespaces.

The second one controls how much it can **use**: / how much CPU, / and how much memory. / Linux calls these cgroups. [pause]

{key} So, / in one line, / a container is a process that sees less, / and is allowed to use less.

But please don't just take my word for it. / Let me show you. [long pause]

=== PART 6. DEMO: A CONTAINER IS JUST A PROCESS ===

[ON SCREEN: demo, a container is just a process]

(DEMO: terminal. docker run --rm -it alpine sh, then ps, then hostname, then exit. Optional: docker run --rm -it --memory 256m alpine sh)

{tr} Let's prove it on a real machine.

I'll start a tiny container with docker run alpine, / and open a shell inside it.

Look how fast that was. / Well under a second.

Now I'll run ps, / to see the process list.

Inside the container, / my own process list has just **two** entries: / the shell, / and ps itself.

The shell even thinks it's process number one. / That's a namespace at work. [pause]

Next, / let's type hostname. / The container has its own hostname, / a random ID, / and not my laptop's name.

Now, / a memory limit. / If I start the container with dash dash memory two hundred fifty six m, / a cgroup caps it at two hundred fifty six megabytes.

Go over that limit, / and the kernel stops the process.

Finally, / I type exit, / and the container is **gone**.

No VM to shut down. / Nothing to clean up. [pause]

So that's what a container is. / Now let's talk about why everyone fell in love with them. [long pause]

=== PART 7. WHY TEAMS LOVE CONTAINERS ===

[ON SCREEN: why teams love containers]

{tr} Containers won because they make life easier for developers, / for operations, / and for the people paying the bill.

First, / **consistency**. / Dev, / test, / and production all run the exact same image. / So goodbye, / works on my machine.

Second, / portability. / The same container runs on Ubuntu, / on bare metal, / or on any major cloud.

Third, / isolation. / Each app brings its own dependencies, / so two apps can need two versions of the same library, / and nobody fights.

Fourth, / density. / Light containers mean more apps per server, / and a smaller bill.

And fifth, / fast delivery. / You build the image once, / test it, / ship it, / and roll back to the previous image if something breaks. [pause]

But, / as you'll see, / they also created a whole new problem. [long pause]

=== PART 8. THE NEW PROBLEM ===

[ON SCREEN: hundreds of containers at scale]

{tr} So, / containers solve **packaging**, / but they don't run themselves.

One container on your laptop is easy.

Now imagine **hundreds of containers**, / spread across dozens of servers, / in production, / at three in the morning.

First, / a crash. / A container dies. / Who restarts it?

Then, / a traffic spike. / Who adds more copies, / and who removes them when it's over?

Then, / a server failure. / A whole machine disappears. / Who moves its containers somewhere healthy?

Next, / you ship a new version. / How do you roll it out with **zero downtime**, / and roll it back if it's bad?

And every time you start a container, / which server has room for it? / That's the question of placement. [pause]

Doing all of that by hand, / with scripts and late-night pages, / simply **doesn't scale**. [pause]

{key} We need something that runs our containers **for** us.

And that's exactly where Kubernetes comes in. [long pause]

=== PART 9. MEET KUBERNETES ===

[ON SCREEN: meet Kubernetes]

{intro} Kubernetes is the system that runs your containers for you.

The name is literally Greek for **helmsman**, / the person who steers the ship.

That's why the logo is a ship's wheel.

You'll often see the nickname K8s, / said "kates". / There are eight letters between the K and the s.

Kubernetes was open-sourced by Google, / in twenty fourteen.

It builds on more than fifteen years of Borg, / which is Google's internal system for running its own workloads at scale.

And in twenty eighteen, / it became the first project to graduate from the Cloud Native Computing Foundation, / the CNCF. [pause]

{def} Kubernetes is a **portable**, / **extensible**, / open source platform / for managing containerized workloads and services. [pause]

In plain words: / you tell it what you want running, / and it **keeps** it running.

So, / how does it actually keep things running? / That's the most important idea in this whole series. [long pause]

=== PART 10. DESIRED STATE ===

[ON SCREEN: desired state and the control loop]

{key} Kubernetes doesn't follow your steps. / It chases the result you describe.

The docs call that result the **desired state**.

It starts with you. / You write down what you want, / in a small text file. / We call it a YAML manifest.

Something like: / three copies of my web app, / using this image, / reachable on this port.

Then a control loop takes over.

The loop watches the cluster, / and asks one question, / over and over. [pause]

Does what's running match what was asked for?

If yes, / it does nothing. / If no, / it acts to close the gap.

So it's observe, / compare, / and act. [pause]

So if one of your three copies crashes, / nobody runs a restart script.

The loop sees **two**, / where you asked for **three**, / and starts a new one. [pause]

{key} The docs put it nicely: / Kubernetes is a set of independent control processes, / constantly driving the **current state** toward the **desired state**.

Now, / I'd rather show you this than just talk about it. / So let's go to the terminal. [long pause]

=== PART 11. DEMO: SELF-HEALING ===

[ON SCREEN: demo, self-healing]

(DEMO: local kind cluster already running. kubectl create deployment web --image=nginx --replicas=3, kubectl get pods, kubectl delete pod <one pod>, kubectl get pods --watch)

{tr} Let's watch a control loop do its job.

I've got a small local cluster running. / We'll build one together in episode three, / so please don't worry about the setup yet.

First, / I'll run kubectl create deployment.

I'm asking for a web app, / using the nginx image, / with **three** replicas.

Then kubectl get pods. / And there they are: / three pods, / all running.

{def} A pod is the smallest thing Kubernetes runs. / For now, / just think of it as a wrapper around a container.

Now, / let's be a bit mean. / I'll run kubectl delete pod, / and pick one of them.

Watch the list. [pause]

The old one is terminating, / and a new pod is created / within a second or two. [pause]

I didn't restart anything, / and I didn't write a script.

{key} The desired state said **three**. / The current state dropped to **two**. / And the control loop **closed the gap**.

So, / self-healing is one thing Kubernetes gives you. / Let's look at the rest. [long pause]

=== PART 12. WHAT KUBERNETES GIVES YOU ===

[ON SCREEN: what Kubernetes gives you]

{tr} Kubernetes answers every one of our three-in-the-morning questions.

First, / self-healing. / Kubernetes restarts crashed containers, / replaces them, / and moves work away from failed servers.

Second, / scaling. / You add or remove copies with one command, / or automatically, / based on CPU usage.

Third, / rollouts and rollbacks. / You ship a new version gradually, / and if it misbehaves, / you go back in one step.

Fourth, / finding and sharing traffic. / Each group of containers gets a **stable name**, / and traffic is spread across the healthy copies. / The docs call this service discovery and load balancing.

Fifth, / fitting containers onto servers. / You say how much CPU and memory each one needs, / and Kubernetes packs them in, / a bit like a game of Tetris. / That's called bin packing.

And sixth, / settings and passwords live **outside** your image, / so you can change them without rebuilding. / That's config and secrets. [pause]

That's a long list. / So, / to be fair, / let me also tell you what Kubernetes is **not**. [long pause]

=== PART 13. WHAT KUBERNETES IS NOT ===

[ON SCREEN: what Kubernetes is not]

{tr} Kubernetes is a set of building blocks, / not a finished product.

It's not a traditional, / all-inclusive platform as a service, / or PaaS.

For example, / it doesn't build your code. / Your build pipeline makes the image, / and Kubernetes runs it.

It has no built-in database, / and no built-in message queue. / You run those on top of it, / or you use a managed service.

And it doesn't force a logging or monitoring tool on you. / You pick one, / and plug it in. [pause]

Honestly, / I think that's a strength. / You decide how to put the building blocks together.

So, / let's put everything we've seen today together. [long pause]

=== PART 14. RECAP AND WHAT'S NEXT ===

[ON SCREEN: recap]

{tr} Let's look back at where we've been. / Physical servers, / then virtual machines, / then containers.

{key} Containers package the app, / with everything it needs, / into one **immutable** image.

{key} Kubernetes runs the fleet. / It places, / heals, / scales, / and updates containers across many servers.

{key} And desired state drives everything. / You declare what you want, / and control loops make it **true**. [pause]

That's really the whole story of today. / Containers make your app run the same everywhere, / and Kubernetes keeps them running the way you asked.

[ON SCREEN: next, Kubernetes architecture]

What's your best "works on my machine" story? / Drop it in the comments. / I read every one.

This is a forty-five episode series, / so if you want to follow it from zero, / subscribe.

Because next time, / we open the hood. / Who actually runs those control loops? / And where do your containers really live?

We'll meet the control plane, / which is the **brain** of the cluster. / And the worker nodes, / where your containers actually run.

See you there. [long pause]
