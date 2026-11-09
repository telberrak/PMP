**Kubernetes from Zero, Episode 6: Namespaces**
(About 11 to 12 minutes with the demo. Format: Detailed, timed at 228 spoken words per minute plus pauses.)
(Voice: conversational-speech-script (no running analogy), adapted with teleprompter-script-adapter.)
(Source: kubernetes.io/docs "Namespaces", "Share a Cluster with Namespaces", "DNS for Services and Pods" and "Namespaces Walkthrough" pages.)
Bold = stress. / = brief pause. [pause] = 1 second. [long pause] = 2 to 3 seconds.

=== PART 1. COLD OPEN ===

[ON SCREEN: one cluster, many teams]

{intro} Two teams. / One cluster. / And both want an app called web. [pause]

So, / who gets the name?

With namespaces, / the answer is: / both of them.

This is episode six, / the last episode of Part one.

The one thing I want you to leave with is this. [pause]

{key} A namespace gives each team its own space in a shared cluster, / but it's a naming boundary, / not a security wall.

We'll cover what a namespace is, / and the ones every cluster starts with. / Then how they shape DNS. / Then, / just as important, / what they **don't** do, / and when you shouldn't use them. / And then two demos.

So, / let's start with the basics. [long pause]

=== PART 2. WHAT IS A NAMESPACE ===

[ON SCREEN: a namespace is a virtual cluster]

{def} A **namespace** divides one cluster into separate groups of resources.

Think of the cluster as an office building, / and the namespaces as its floors.

Every floor can have a meeting room called Blue. / But on any one floor, / there's only one room called Blue.

{key} And that's the core rule. / Names must be unique **within** a namespace, / but not **across** namespaces.

So the team on the dev floor can run a deployment called web. / And the team on the prod floor can run their own deployment called web. / No conflict. [pause]

A namespace name follows the DNS rules. / Lowercase letters, / numbers, / and dashes, / up to sixty-three characters.

One small detail. / Kubernetes automatically adds a label to every namespace, / with its own name. / That makes namespaces easy to select later.

Namespaces also give you a place to hang rules. / Who can do what, / and how much they can use. / We'll get to those later in the series.

Now, / a new cluster isn't empty on day one. [long pause]

=== PART 3. THE FOUR STARTING NAMESPACES ===

[ON SCREEN: the four starting namespaces]

{tr} Every new cluster starts with four namespaces, / and you should know what each one is for.

The first is default. / It's where your objects go when you don't name a namespace. / Everything we've done so far, / we did in default.

The second is kube-system. / That's home to the objects Kubernetes creates for itself.

Remember the API server, / etcd, / CoreDNS, / and kube-proxy from episode two? / They live in kube-system.

My advice: / look, / but don't touch. / Never deploy your own apps into kube-system.

The third is kube-public. / It's readable by everyone, / even clients that haven't logged in. / It's mostly kept for cluster-wide information.

And the fourth is kube-node-lease. / It holds one small object for every node, / called a Lease. / The kubelet updates it as a heartbeat, / so the control plane knows each node is still alive. [pause]

{key} One rule from the docs. / Don't create your own namespaces starting with kube dash. / That prefix is reserved for Kubernetes.

Now, / here's something that surprises a lot of people. [long pause]

=== PART 4. NAMESPACED OR CLUSTER-WIDE ===

[ON SCREEN: namespaced vs cluster-wide]

{tr} Not every object lives in a namespace.

Most of the things you create day to day are namespaced. / Pods, / deployments, / services, / ConfigMaps, / and secrets.

But some objects belong to the whole cluster. / We call them cluster-wide.

Nodes, / for example. / A node isn't owned by one team. / It belongs to the cluster.

The same goes for persistent volumes, / storage classes, / and namespaces themselves. / A namespace can't live inside another namespace.

{key} If you're ever unsure, / run kubectl api-resources, / with dash dash namespaced equals true, / or false. / It tells you which types are namespaced.

Next, / namespaces also change how your apps find each other. [long pause]

=== PART 5. NAMESPACES AND DNS ===

[ON SCREEN: namespaces and DNS]

{tr} Every service gets a DNS name, / and that name includes its namespace.

It's built from three parts. / The service name, / the namespace, / and then svc dot cluster dot local.

So a service called web, / in the prod namespace, / is web dot prod dot svc dot cluster dot local. [pause]

From a pod in the **same** namespace, / you just say web.

From a **different** namespace, / you add the namespace. / So, / web dot prod.

The docs add a subtle warning here. / Avoid naming a namespace after a public top-level domain, / like com, / or org. / Otherwise a short name inside the cluster could point to your service, / instead of the real website.

{key} Here's a common gotcha. / Your app works perfectly in one namespace, / then fails in another, / because it used a short name that now points somewhere else.

Now, / this next part is the one I really want you to remember. [long pause]

=== PART 6. WHAT NAMESPACES DON'T DO ===

[ON SCREEN: what namespaces don't do on their own]

{tr} A namespace on its own is a **naming boundary**, / not a security wall.

So by default, / pods in one namespace can talk to pods in any other namespace. / And a user with broad permissions can see across all of them. [pause]

To turn namespaces into real isolation, / you add three other features.

First, / RBAC, / which controls who can do what, / in which namespace. / That's episode thirty-nine.

Second, / resource quotas, / which cap how much CPU, / memory, / and how many objects each namespace can use. / That's episode twenty-six.

And third, / network policies, / which control which pods can talk to which. / That's episode twenty-one.

So, / when should you actually use namespaces? [long pause]

=== PART 7. WHEN TO USE NAMESPACES ===

[ON SCREEN: when to use namespaces]

{tr} Honestly, / the docs are refreshingly direct about this.

{key} Namespaces are meant for clusters with **many users**, / spread across many teams, / or projects.

If you only have a few to tens of users, / you may not need to create them at all.

Good reasons to create one are a separate team, / a separate project, / or a separate stage, / like dev and prod, / in a shared cluster.

In practice, / you'll see three common patterns. / One namespace per team. / One per environment. / Or one per application.

But please don't use namespaces to tell apart slightly different versions of the same app. / For that, / use labels. [pause]

{key} So, / big boundaries, / use namespaces. / Small differences, / use labels.

Now, / let's look at the commands you'll actually type. [long pause]

=== PART 8. WORKING WITH NAMESPACES ===

[ON SCREEN: working with namespaces]

{tr} You only need a handful of commands to work with namespaces.

kubectl get namespaces lists them all. / The short name is ns.

kubectl create namespace, / and a name, / creates one. / For example, / dev.

Dash n, / and a name, / runs any command in that namespace. / And dash capital A runs it across all of them.

But typing dash n every time gets old fast. / So you can change your default namespace.

kubectl config set-context, / dash dash current, / dash dash namespace equals dev. / From then on, / dev is your default. [pause]

And just like kubectx for contexts, / there's a small helper called kubens, / that switches your default namespace with one short command.

In YAML, / you can set the namespace in the metadata. / But many teams leave it out, / and choose it when they apply. / That way, / the same file works everywhere.

Before the demo, / one warning. / And it's a big one. [long pause]

=== PART 9. DELETING A NAMESPACE ===

[ON SCREEN: deleting a namespace deletes everything in it]

{tr} Deleting a namespace deletes **everything** inside it.

kubectl delete namespace, / and a name, / removes the namespace, / and every deployment, / every pod, / every service, / and every secret inside it. [pause]

While that happens, / the namespace shows a status of Terminating. / It waits until everything inside has been cleaned up.

Sometimes a namespace gets stuck in Terminating. / That's usually a finalizer, / waiting for something that will never finish. / We'll take finalizers apart in episode fourteen.

{key} So, / namespaces are perfect for throwaway environments. / And very dangerous in production. / Please, / always check the name twice, / because once it's gone, / it's gone.

Right. / Let's try all of this for real. [long pause]

=== PART 10. DEMO: TWO NAMESPACES, ONE NAME ===

[ON SCREEN: demo, two namespaces and one name]

(DEMO: kind cluster k8s-zero. kubectl get ns; kubectl get pods -n kube-system; kubectl create ns dev; kubectl create ns prod; kubectl create deployment web --image=nginx -n dev; kubectl create deployment web --image=nginx -n prod; kubectl get deploy -A; kubectl config set-context --current --namespace=dev; kubectl get deploy)

{tr} First, / kubectl get ns. / There are our four starting namespaces, / plus a local-path-storage namespace that kind adds.

Then, / kubectl get pods, / dash n kube-system. / The control plane, / CoreDNS, / and kube-proxy, / just as I promised.

Now, / kubectl create ns dev, / and kubectl create ns prod.

I'll create a deployment called web, / in dev. / And another deployment called web, / in prod.

Same name, / twice. / And no error.

Now, / kubectl get deploy, / dash capital A. / Two deployments called web, / side by side, / one per namespace. [pause]

Finally, / I'll make dev my default, / with kubectl config set-context. / And now a plain kubectl get deploy shows only the dev one.

So that's two namespaces, / and one name. / Now let's see how they reach each other. [long pause]

=== PART 11. DEMO: DNS ACROSS NAMESPACES ===

[ON SCREEN: demo, DNS across namespaces]

(DEMO: kubectl expose deployment web --port=80 -n prod; kubectl run tmp --image=busybox -n dev -it --rm --restart=Never -- wget -qO- web.prod; then wget -qO- web (fails or hits dev); kubectl delete ns dev prod; kubectl config set-context --current --namespace=default)

{tr} First, / I'll expose the prod deployment as a service called web.

Then I'll start a temporary busybox pod, / in the dev namespace, / and call web dot prod.

There's the nginx welcome page. / It's coming from prod, / even though I'm in dev.

Now, / let's call just web, / the short name. / It fails. / There's no service called web in dev. [pause]

{key} So, / short names stay inside your own namespace. / Adding the namespace takes you to another one.

Finally, / let's clean up. / kubectl delete ns, / dev and prod. / Both namespaces terminate, / and everything inside goes with them.

And I'll set my default back to the default namespace.

So, / let's wrap up Part one. [long pause]

=== PART 12. RECAP ===

[ON SCREEN: recap]

{tr} Let's recap the last topic of Part one.

{key} A namespace divides one cluster into groups. / Names are unique inside a namespace, / but not across them.

{key} Every cluster starts with default, / kube-system, / kube-public, / and kube-node-lease.

{key} Services get DNS names that include the namespace. / And on its own, / a namespace is not a security wall.

{key} Use namespaces for teams and environments. / And use labels for small differences. [pause]

And the thing I asked you to leave with: / a namespace gives each team its own space in a shared cluster, / but it's a naming boundary, / not a security wall. [long pause]

=== PART 13. WHAT'S NEXT ===

[ON SCREEN: next, Part 2 workloads]

How do you split your clusters: / by team, / by environment, / or by app? / Share it in the comments.

That's the end of Part one, / the foundations. / You now know why Kubernetes exists, / how it's built, / how to run it, / how to talk to it, / and how its objects work.

Next time, / we start Part two: / workloads. / And we begin with the smallest thing Kubernetes runs: / the pod. / Its lifecycle, / its phases, / and its restart policies.

See you there. [long pause]
