**Kubernetes from Zero, Episode 4: kubectl Essentials, and How to Read and Write YAML Manifests**
(About 11 to 12 minutes with the demo. Format: Detailed, timed at 228 spoken words per minute plus pauses.)
(Voice: conversational-speech-script (no running analogy), adapted with teleprompter-script-adapter.)
(Source: kubernetes.io/docs "Command line tool (kubectl)", "kubectl Quick Reference", "Kubernetes Object Management", "Objects In Kubernetes" and "Managing Resources" pages.)
Bold = stress. / = brief pause. [pause] = 1 second. [long pause] = 2 to 3 seconds.

=== PART 1. COLD OPEN ===

[ON SCREEN: commands or YAML]

{intro} Every Kubernetes engineer lives in two places: / a terminal, / and a YAML file. [pause]

In the terminal, / you type commands. / They're quick and direct, / and they're gone the moment you press enter.

In the YAML file, / you write down what you want, / and you keep it.

This is episode four, / and today you'll get comfortable in both places.

Here's the rule of thumb I'll keep coming back to. [pause]

{key} Use commands to look around, / and use YAML files with kubectl apply to change things.

First, / we'll learn how a kubectl command is built. / Then the everyday commands. / Then YAML, / line by line. / And then we'll try it all on our cluster.

So, / let's start with the shape of a command. [long pause]

=== PART 2. THE SHAPE OF A KUBECTL COMMAND ===

[ON SCREEN: the shape of a kubectl command]

{tr} Almost every kubectl command reads like a short sentence.

It starts with kubectl itself.

Then a command. / What do you want to do? / For example, / get.

Then a type. / What kind of thing? / For example, / pods.

Then, / if you need it, / a name. / Which one? / Say, / web.

And finally, / flags, / which change how the command behaves.

A very common flag is dash n, / for namespace. / So dash n dev means, / look in the dev namespace. [pause]

Its big brother is dash capital A, / which means all namespaces at once. / We'll go deep on namespaces in episode six.

{key} So kubectl get pods web, / dash n dev, / really reads like a sentence. / "Get the pod called web, / in dev."

Now that you know the shape, / let's fill it with the commands you'll use every day. [long pause]

=== PART 3. THE EVERYDAY COMMANDS ===

[ON SCREEN: the everyday commands]

{tr} Honestly, / five commands cover most of your day.

First, / get. / It lists things, / short and quick. / For example, / kubectl get pods.

Second, / describe. / It shows everything about one object, / including recent events. / When something's broken, / describe is my first stop.

Third, / logs. / It prints what a container wrote to its output. / Add dash f to follow the logs live.

Fourth, / exec. / It runs a command inside a running container. / With dash i t, / you get an interactive shell.

And fifth, / delete. / It removes an object, / so please be careful with that one. [pause]

{key} If you want a short way to remember it: / get to look, / describe to investigate, / and logs and exec to get inside.

Now, / get can show you the same data in different shapes. [long pause]

=== PART 4. OUTPUT FORMATS ===

[ON SCREEN: output formats]

{tr} With dash o, / you choose how much detail you see.

Dash o wide adds extra columns, / like the pod's IP address, / and the node it runs on.

Dash o yaml prints the full object, / exactly as the cluster stores it.

Dash o json does the same, / but in JSON.

And dash o jsonpath pulls out a single field. / For example, / just the pod's IP, / which is perfect for scripts. [pause]

{key} Here's a habit worth building. / When you meet a new kind of object, / look at it with dash o yaml. / You'll learn a lot about how Kubernetes sees it.

So, / how do you find your way around all these types? [long pause]

=== PART 5. FINDING YOUR WAY AROUND ===

[ON SCREEN: finding your way around]

{tr} Three small tricks will save you a lot of typing, / and a lot of guessing.

First, / short names. / Instead of pods, / you can type po. / Deployments become deploy. / And services become svc.

Second, / kubectl api-resources. / It lists every type your cluster knows, / with its short name, / and whether it lives in a namespace.

Third, / and this one's my favourite, / kubectl explain.

It's documentation, / built right into your terminal.

For example, / kubectl explain deployment dot spec dot replicas / tells you exactly what that field is, / and what kind of value it takes. [pause]

{key} So you never have to guess a field name again.

Now, / let's step back, / and look at the bigger choice: / how you actually manage your objects. [long pause]

=== PART 6. THREE WAYS TO MANAGE OBJECTS ===

[ON SCREEN: three ways to manage objects]

{tr} The docs describe three ways to manage objects, / and for real work, / I'd always pick the third.

The first is imperative commands. / You say exactly what to do, / right in the terminal, / like kubectl create deployment, / or kubectl scale.

It's fast and great for experiments. / But nothing is written down.

The second is imperative object config. / You write the object in a YAML file, / and then tell kubectl what to do with it, / like kubectl create dash f.

And the third is declarative object config, / using kubectl apply.

You keep YAML files that describe what you want. / You run kubectl apply, / and Kubernetes works out what to create, / change, / or leave alone. [pause]

{key} That's the one I'd use for real work. / Your files can live in Git, / get reviewed, / and be applied again and again, / with the same result.

So, / to write those files, / we need just enough YAML. [long pause]

=== PART 7. YAML IN SIXTY SECONDS ===

[ON SCREEN: YAML in sixty seconds]

{tr} The good news is, / you only need a tiny bit of YAML.

It's built from pairs: / a key, / a colon, / a space, / and the value.

Structure comes from indentation. / Nested keys are indented under their parent, / using spaces.

{key} And I really mean spaces. / **Never tabs.** / One single tab, / and the whole file is rejected.

Lists use a dash at the start of each line. / So a list of containers is just a few lines, / each one starting with a dash. / Those are the list items.

And three dashes on their own line separate documents. / So one file can hold a deployment and a service, / as two separate documents. [pause]

Honestly, / that's most of the YAML you'll ever need for Kubernetes.

Now, / every Kubernetes manifest starts the same way. [long pause]

=== PART 8. THE FOUR REQUIRED FIELDS ===

[ON SCREEN: the four required fields]

{key} Every Kubernetes manifest starts with the same four fields.

First, / apiVersion. / It says which version of the Kubernetes API this object belongs to. / For a deployment, / that's apps slash v one.

Second, / kind. / What type of object is this? / A Deployment, / a Service, / or a Pod.

Third, / metadata. / That's what identifies the object: / its name, / its namespace, / and its labels.

And fourth, / spec.

{def} The spec is your **desired state**. / It's where you describe what you want this object to be.

And it's different for every kind. / A pod's spec lists containers. / A deployment's spec has replicas, / and a pod template. [pause]

{key} apiVersion, / kind, / metadata, / and spec. / Learn those four, / and every manifest suddenly looks familiar.

So, / let's read a real one together. [long pause]

=== PART 9. READING A DEPLOYMENT MANIFEST ===

[ON SCREEN: reading a deployment manifest]

{tr} Let's read a real Deployment manifest, / from top to bottom.

apiVersion, / apps slash v one. / Kind, / Deployment. / And in the metadata, / the name is web.

Then the spec. / Replicas, / three. / That's how many copies we want.

Then a selector, / with match labels, / app equals web. / This tells the deployment which pods belong to it.

Then the pod template. / Think of it as the blueprint for every pod this deployment creates.

The template has its own metadata, / with the same label, / app equals web. / And those labels **must** match the selector.

Inside the template's spec, / there's a list of containers. / One container, / called web, / using the container image nginx, / on port eighty. [pause]

{key} Notice the nesting. / A deployment contains a template, / and that template contains a pod. / Objects inside objects.

Here's where most of us trip up, / at least once.

One, / the template labels don't match the selector. / The cluster rejects it, / and tells you they don't match.

Two, / the containers list is indented one level too far, / or not far enough. / So if an error makes no sense, / check your indentation first.

Now, / here's a small secret. [long pause]

=== PART 10. GENERATE, DON'T TYPE ===

[ON SCREEN: generate, look up, validate]

{tr} Experienced engineers rarely write YAML from scratch.

First, / they generate it, / with dry-run.

kubectl create deployment web, / dash dash image nginx, / dash dash dry-run equals client, / dash o yaml.

Nothing is created. / kubectl just prints the YAML it would have sent. / Save that into a file, / and you have a perfect starting point.

Second, / when they need a field, / they look it up with explain.

And third, / before applying a change, / they check it with diff. / kubectl diff shows exactly what would change in the live cluster. [pause]

There's also dry-run equals server. / It sends your YAML to the cluster for a full check, / but saves nothing.

So, / enough talking. / Let's do it for real. [long pause]

=== PART 11. DEMO: APPLY, GET AND DESCRIBE ===

[ON SCREEN: demo, apply, get and describe]

(DEMO: kind cluster k8s-zero. kubectl create deployment web --image=nginx --replicas=3 --dry-run=client -o yaml > web.yaml; open web.yaml; kubectl apply -f web.yaml; kubectl get deploy,po -o wide; kubectl describe deploy web)

{tr} We're on the cluster we built last episode.

I'll generate a manifest with dry-run, / and save it as web.yaml.

Let's open web.yaml. / There's apiVersion, / kind, / metadata, / and spec. / Exactly what we just read.

Now, / kubectl apply dash f web.yaml. / Deployment web, / created.

Next, / kubectl get, / for the deployment and the pods, / with dash o wide. / One deployment, / and three pods, / spread over our two workers.

Now, / kubectl describe deploy web.

At the bottom, / the events. / The deployment controller scaled up a ReplicaSet. / That's the same story we followed in episode two.

Now let's change the file. [long pause]

=== PART 12. DEMO: CHANGE, DIFF AND RE-APPLY ===

[ON SCREEN: demo, change, diff and re-apply]

(DEMO: edit web.yaml replicas: 3 to 5; kubectl diff -f web.yaml; kubectl apply -f web.yaml; kubectl get pods; kubectl logs <pod>; kubectl exec -it <pod> -- sh; exit)

{tr} This is where declarative really shines.

I'll edit the file, / and change replicas from three to five.

Before applying, / kubectl diff. / One line changed. / Replicas, / from three to five. [pause]

Now, / kubectl apply, / again. / Deployment web, / configured. / Not created. / Configured.

And kubectl get pods. / Five pods.

{key} Notice, / I didn't tell Kubernetes to add two pods. / I just changed the desired state, / and apply worked out the rest.

Quickly, / logs. / kubectl logs, / and one pod name. / There's nginx, / starting up.

And exec. / kubectl exec, / dash i t, / the pod name, / dash dash, / s h. / Now I'm inside the container. / I type exit, / and I'm back.

Before we wrap up, / four small tips that will make your life easier. [long pause]

=== PART 13. PRODUCTIVITY TIPS ===

[ON SCREEN: productivity tips]

{tr} These four tips will save you hours.

One. / Create an alias. / Alias k equals kubectl. / Trust me, / you'll type it thousands of times.

Two. / Turn on shell completion. / Run kubectl completion, / for bash or zsh, / and the tab key finishes names for you.

Three. / Use watch mode. / Add dash w to kubectl get, / and the list updates live.

And four. / Clean up with delete. / kubectl delete dash f web.yaml removes everything that file created.

So, / let's bring it all together. [long pause]

=== PART 14. RECAP ===

[ON SCREEN: recap]

{tr} Quick recap.

{key} A kubectl command is a sentence: / command, / type, / name, / and flags.

{key} Get, / describe, / logs, / and exec cover most of your day.

{key} Every manifest has four fields: / apiVersion, / kind, / metadata, / and spec.

{key} And for real work, / write it down. / Keep your YAML in files, / and use kubectl apply. [pause]

And our rule of thumb from the start: / use commands to look around, / and use YAML files with kubectl apply to change things. [long pause]

=== PART 15. WHAT'S NEXT ===

[ON SCREEN: next, the Kubernetes API and objects]

What's the one kubectl command you use most? / Put it in the comments.

If this was useful, / the subscribe button really helps the channel.

Today, / we wrote the spec, / the part we control. / But every object has a second half, / that Kubernetes writes back to us. / We call it the status.

Next time, / we look at spec versus status, / and at the labels that tie everything together.

See you there. [long pause]
