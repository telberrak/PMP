**Kubernetes from Zero, Episode 5: The Kubernetes API and Objects, Spec vs Status, Labels, Selectors and Annotations**
(About 11 to 12 minutes with the demo. Format: Detailed, timed at 228 spoken words per minute plus pauses.)
(Voice: conversational-speech-script (no running analogy), adapted with teleprompter-script-adapter.)
(Source: kubernetes.io/docs "Objects In Kubernetes", "The Kubernetes API", "Object Names and IDs", "Labels and Selectors", "Recommended Labels", "Annotations" and "Field Selectors" pages.)
Bold = stress. / = brief pause. [pause] = 1 second. [long pause] = 2 to 3 seconds.

=== PART 1. COLD OPEN ===

[ON SCREEN: what you asked for vs what you got]

{intro} Every object in Kubernetes has two halves. [pause]

What you asked for, / and what you actually got.

You write the first half. / Kubernetes writes the second. / And the whole system exists to make those two halves match.

Welcome back. / This is episode five, / and today we look inside a Kubernetes object.

Two halves, / written by two different authors. / In one line: [pause]

{key} You write what you want in the spec, / Kubernetes writes what it got in the status, / and labels tie everything together.

We'll start with what an object is. / Then spec and status. / Then the API, / names, / labels, / and annotations. / And finally, / two demos, / including a little magic trick.

So, / first things first. [long pause]

=== PART 2. WHAT IS A KUBERNETES OBJECT ===

[ON SCREEN: an object is a record of intent]

{def} A Kubernetes object is a record of what you want, / and Kubernetes keeps working to make it true.

The docs call it a **record of intent**. / More formally, / it's a lasting entry, / stored in etcd, / that describes part of your cluster.

A deployment is an object. / So is a pod, / a service, / and a namespace.

Once you create one, / Kubernetes constantly works to make sure it exists, / and looks the way you described.

You usually write objects in YAML. / kubectl turns that YAML into JSON, / and sends it to the API server. / The API server checks it, / and stores it in etcd.

And every object is made of the same three building blocks. / The metadata, / the spec, / and the status. [pause]

So, / let's look at the two halves that matter most. [long pause]

=== PART 3. SPEC VS STATUS ===

[ON SCREEN: spec vs status]

{def} The spec is what you want, / and the status is what you've got.

The spec is the desired state. / You write it.

{def} The status is the current state. / Kubernetes writes it, / and keeps it up to date.

Between the two sits a controller, / running its control loop. / It reads the spec, / looks at the status, / and acts to close the gap. [pause]

Let's make that concrete. / Take a deployment with three replicas in its spec.

Its status says how many replicas exist right now, / how many are ready, / and how many are up to date.

If the status says two, / and the spec says three, / the controller gets to work.

Here's a detail worth knowing. / Every time you change the spec, / a counter in the metadata, / called generation, / goes up by one.

And the status has its own counter, / called observedGeneration. / When the two numbers match, / you know the controller has seen your latest change.

{key} So, / you never edit the status yourself. / It's the cluster's answer, / not your request.

Now, / all these objects live behind one API. / Let's see how it's organised. [long pause]

=== PART 4. THE KUBERNETES API ===

[ON SCREEN: the Kubernetes API, groups and versions]

{tr} The apiVersion line in your YAML tells Kubernetes which group, / and which version, / your object belongs to.

All objects live behind one API, / served by the API server. / Every type of object has its own address.

And those types are organised into API groups.

The original group is called the core group. / It holds the oldest types, / like pods, / services, / and namespaces. / Its apiVersion is just v one.

Newer types live in named groups. / Deployments live in the apps group, / so their apiVersion is apps slash v one.

Each group also has versions. / Alpha versions can change, / or disappear. / Beta versions are well tested. / And plain v one means stable. [pause]

Kubernetes follows a deprecation policy. / Before an old version is removed, / you get warnings, / and time to move.

That's why every upgrade guide says, / check your apiVersions.

{key} And if you're curious, / run kubectl api-versions, / to see every one your cluster serves.

Next, / how does Kubernetes tell one object from another? [long pause]

=== PART 5. NAMES AND UIDS ===

[ON SCREEN: names and UIDs]

{tr} Every object has two identities: / a name you choose, / and an ID Kubernetes gives it.

First, / the name. / A name is unique for each kind, / inside a namespace.

So you can have a pod called web, / and a service called web. / But not two pods called web, / in the same namespace.

Most names must be valid DNS names: / lowercase letters, / numbers, / dashes, / and dots. / So no capitals, / and no underscores.

Second, / the UID.

{def} The UID is generated by Kubernetes, / and it's unique for the cluster's whole lifetime.

So if you delete a pod called web, / and create a new one with the same name, / it gets a brand-new UID. / Same name, / different object.

Now, / names are useful. / But the real flexibility in Kubernetes comes from somewhere else. [long pause]

=== PART 6. LABELS ===

[ON SCREEN: labels are sticky notes]

{tr} **Labels** are what make Kubernetes so flexible.

{def} Labels are key and value pairs that you stick on objects, / to describe things that mean something to **you**.

Think of them as sticky notes.

So one pod might carry app equals web, / tier equals frontend, / and environment equals production.

Another might carry app equals api, / tier equals backend, / and environment equals production.

Kubernetes doesn't care what your labels mean. / But it lets you pick objects by them. [pause]

Teams use labels for all kinds of things. / Which release, / stable or canary. / Which environment, / dev, / QA, / or production. / Or which team owns it.

A quick rule. / A label key can have an optional prefix, / then a slash, / then a name. / The name and the value can each be up to sixty-three characters.

And the docs recommend a shared set of labels, / all under the app dot kubernetes dot io prefix. / Things like name, / instance, / version, / and part-of.

But labels on their own are just notes. / What makes them powerful is the next part. [long pause]

=== PART 7. SELECTORS ===

[ON SCREEN: selectors find objects by label]

{tr} **Selectors** find objects by their labels.

{def} A label selector picks out a group of objects, / based on the labels they carry.

There are two styles. / First, / equality. / Equals, / or not equals. / For example, / environment equals production.

Second, / set-based. / That's in, / not in, / and exists. / For example, / environment in production or QA.

On the command line, / you use dash l. / So, / kubectl get pods, / dash l, / app equals web. [pause]

{key} And here's the thing. / This is how Kubernetes connects objects to each other.

A service finds its pods with a selector. / A deployment finds the pods it owns with a selector.

{key} So objects connect by labels, / not by names. / Change the labels, / and you change what belongs together. [pause]

One small detail. / Services only support the equality style. / Deployments, / ReplicaSets, / and Jobs also accept the set-based style.

Now, / labels have a quieter cousin. [long pause]

=== PART 8. ANNOTATIONS ===

[ON SCREEN: annotations are notes for tools]

{tr} Annotations are notes for tools and people, / not for grouping.

They look just like labels. / Keys and values, / in the metadata.

But there's one big difference. / You **can't** select by annotations.

{def} Annotations hold extra information, / for tools, / libraries, / and humans.

Things like the git commit an image was built from, / a build time, / or who to call when it breaks.

Many tools store their settings in annotations, / and kubectl does too. [pause]

One you'll meet soon is kubernetes dot io slash change-cause. / Put a short message there, / and it shows up in your rollout history, / as the reason for each change.

{key} So here's my rule of thumb. / If you'll ever want to select by it, / make it a label. / If it's just information, / make it an annotation.

There's one last way to filter. [long pause]

=== PART 9. FIELD SELECTORS ===

[ON SCREEN: field selectors]

{tr} Field selectors filter by a field's value, / instead of a label.

{def} For example, / kubectl get pods, / dash dash field-selector, / status dot phase equals Running.

Or you can list every pod on one node, / using spec dot nodeName.

Only a small set of fields is supported, / so labels are still your main tool. / But field selectors are handy in a pinch.

Right. / Let's see all of this on our own cluster. [long pause]

=== PART 10. DEMO: SPEC AND STATUS ===

[ON SCREEN: demo, spec and status]

(DEMO: kind cluster k8s-zero with deployment web from episode 4. kubectl get deploy web -o yaml; point at spec.replicas and status.readyReplicas / conditions. kubectl scale deploy web --replicas=4; kubectl get deploy web -o yaml again.)

{tr} Our deployment web, / from last episode, / is still running.

I'll run kubectl get deploy web, / dash o yaml.

Let's scroll down. / Here's the spec. / Replicas, / five. / That's what we asked for.

Keep scrolling, / to the very bottom. / Here's the status.

Ready replicas, / five. / Available replicas, / five. / And a list of conditions, / like Available, / with a reason and a timestamp.

We never wrote any of that. / The deployment controller did. [pause]

Now, / kubectl scale, / to four replicas. / And then dash o yaml again.

The spec says four. / And a moment later, / the status catches up. / Four ready.

So that's spec and status, / live. / Now for the magic trick. [long pause]

=== PART 11. DEMO: LABELS, SELECTORS AND THE MAGIC TRICK ===

[ON SCREEN: demo, labels and the magic trick]

(DEMO: kubectl get pods --show-labels; kubectl get pods -l app=web; kubectl label pod <one pod> app=debug --overwrite; kubectl get pods --show-labels; kubectl annotate deploy web team=platform; kubectl describe deploy web | grep -i annotations)

{tr} First, / kubectl get pods, / dash dash show-labels.

Every pod carries app equals web, / plus a pod-template-hash label the deployment added.

Now, / kubectl get pods, / dash l, / app equals web. / Only our web pods. [pause]

Here's the trick. / I'll take one of these pods, / and change its label from app equals web, / to app equals debug.

Then, / let's list the pods again. [pause]

There are now **five** pods.

The pod I relabeled is still running. / But it no longer matches the selector, / so the ReplicaSet doesn't count it any more.

It saw only three, / so it created a new pod.

{key} And that's a real technique. / You can pull a misbehaving pod out of rotation, / keep it alive, / and debug it in peace.

Finally, / kubectl annotate deploy web, / team equals platform. / And describe shows the annotation, / right there in the metadata.

Before we wrap up, / let me pull the main ideas together. [long pause]

=== PART 12. RECAP ===

[ON SCREEN: recap]

{tr} Let's pull it together.

{key} Every object has metadata, / a spec you write, / and a status Kubernetes writes.

{key} The apiVersion names the group and the version. / Names are unique per kind and namespace. / And UIDs are unique forever.

{key} Labels identify. / Selectors group. / And annotations carry information for tools. [pause]

Two halves, / two authors. / You write what you want in the spec, / Kubernetes writes what it got in the status, / and labels tie everything together. [long pause]

=== PART 13. WHAT'S NEXT ===

[ON SCREEN: next, namespaces]

Have you ever used the relabel trick to debug a pod? / I'd like to hear how it went.

And if you're following the series, / make sure you're subscribed.

Here's something you might not have noticed. / We've been working in one namespace all along.

So next time, / we look at namespaces. / How to split one cluster into many virtual ones, / and when you honestly shouldn't bother.

See you there. [long pause]
