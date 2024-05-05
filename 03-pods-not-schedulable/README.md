In Kubernetes, the scheduler is responsible for assigning pods to nodes in the cluster based on various criteria. Sometimes, you might encounter situations where pods are not being scheduled as expected. This can happen due to factors such as node constraints, pod requirements, or cluster configurations.

1. Node Selector

- It is a field in a Kubernetes pod and this field will help the nodes or tell the kube scheduler like schedule the pod
only on the particular node.
- We identify that particular node within the deployment.yaml file we will provide a certain labell.
- IF there is any node with that particular label only then  schedule the pod on that particular node.
- IF there is noo node then keep my pod in the non schedulable status 
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/d3805fa6-68c4-490f-96b2-8f7c9c923da7)
- In some cases we have this type of requirement like this pod shoudl run on this particualar node like some applications should run on ARM process so we need ARM node running there to run this particular pod or deployment.
- IF we have applied the deployment AND PODS ARE IN NOT IN PENDING STATE THAT MEANS NODE SELECTOR THAT WE HAVE MENTIONEEDD IN THE DEPLOYMENT.YAML FILE IS WRONG.
- How to find the fix THE ERROR IS 1ST WE NEED TO CHECK FOR THE PENDING STATE OF PODS AND WE NEED TO DESRIBE THAT POD TO FIND THE ERROR
```
root@pavan-virtualbox:/home/pavan/K8S# kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-867798947d-6zg49   0/1     Pending   0          53s
nginx-deployment-867798947d-74rq9   0/1     Pending   0          53s
```
```
kubectl describe pod nginx-deployment-867798947d-6zg49
```
```
ERROR :

Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  81s   default-scheduler  0/3 nodes are available: 1 node(s) had untolerated taint {node-role.kubernetes.io/control-plane: }, 2 node(s) didn't match Pod's node affinity/selector. preemption: 0/3 nodes are available: 3 Preemption is not helpful for scheduling.
```
### HOW TO FIX THIS ERRO
- We need to set the correct name in the node-selector in yaml file and in tthe NODE AS WELL.
- 1ST WE NEED TO EDIT THE NODE AND SET FOR ARM NODE
- ``` kkubectl edit node pavan-multi-worker ```
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/422ef08a-68fa-4cae-8d90-f6ce8094e87d)
- Here we need to node-name in the label secction.
- THis can be done by using kubectl edit orr
- ``` kubectl lable node ```
- Name should match with the deployment.yaml file
- Now pods are in running state
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/b7904311-d42d-4bc5-8026-8f5b20bb33de)

- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/91e5444b-fd35-48c4-a64c-edbff8bbf40c)


**Node Selector is a simple way to constrain pods to nodes with specific labels. It allows you to specify a set of key-value pairs that must match the node's labels for a pod to be scheduled on that node.
Usage: Include a nodeSelector field in the pod's YAML definition to specify the required labels.**

```
spec:
    containers:
    - name: my-app
    image: my-image
    nodeSelector:
    disktype: ssd
```

2. Node Affinity

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/71f69465-dea3-446f-b901-38306ed407ad)

- Node Selector - WIth NOde selector we are forcing the scheduler that schedule the pod only if you find the right node, It's a Hard match. IN the above example if you don't find a arm-processer node.
- NOde Affinity - It is a feature It has 2 OPTIONS 1. PREFERRED 2.REQUIRED.
- REQUIRED - Works almost similar to the node selector where you are telling the scheduler that only schedule if i find the 100% exact match or exact required node, Where as
- PREFERRED - Scheduling option what we are telliing the scheduler is that is first try to find exact same node where i will provide you the lable if you find the lable just use that particular node if you don't find them I am okay you can schedule it anywhere, THis is difference between AFFINITTY AND SELECTOR.

**Node Affinity is a more expressive way to specify rules about the placement of pods relative to nodes' labels. It allows you to specify rules that apply only if certain conditions are met.
Usage: Define nodeAffinity rules in the pod's YAML definition, specifying required and preferred node selectors.**



```
spec:
    containers:
    - name: my-app
    image: my-image
    affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
            operator: In
            values:
            - ssd
```

- SO HERE WE HAVE USED 03-pods-not-schedulable/03-node-affinity-preferred.yaml MANIFEST FILE.
```
spec:
      affinity:
       nodeAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
          preference:
           matchExpressions:
           - key: arch
             operator: In
             values:
             - windows
```
- WE KNOW THAT WE DONT HAVE ANY NODE WITH THIS NAME
- AS WE ARE USING PERFERRED SCHEDULER,  1ST IT WILL CHECK FOR NODE WITH ARCH IT IS NOT AVAILABLE THEN IT WILL SCHEDULE ON ANY AVAILABLE NODE.
```
root@pavan-virtualbox:/home/pavan/K8S# kubectl get pods 
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-797b57bbdb-955bt   0/1     ContainerCreating   0          18s
nginx-deployment-797b57bbdb-klqrg   0/1     ContainerCreating   0          18s
nginx-deployment-797b57bbdb-tmw7l   0/1     ContainerCreating   0          18s
root@pavan-virtualbox:/home/pavan/K8S# kubectl get pods -o wide
NAME                                READY   STATUS              RESTARTS   AGE   IP           NODE                  NOMINATED NODE   READINESS GATES
nginx-deployment-797b57bbdb-955bt   1/1     Running             0          34s   10.244.2.3   pavan-multi-worker    <none>           <none>
nginx-deployment-797b57bbdb-klqrg   1/1     Running             0          34s   10.244.2.2   pavan-multi-worker    <none>           <none>
nginx-deployment-797b57bbdb-tmw7l   0/1     ContainerCreating   0          34s   <none>       pavan-multi-worker2   <none>           <none>
root@pavan-virtualbox:/home/pavan/K8S# kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-797b57bbdb-955bt   1/1     Running   0          39s
nginx-deployment-797b57bbdb-klqrg   1/1     Running   0          39s
nginx-deployment-797b57bbdb-tmw7l   1/1     Running   0          39s
root@pavan-virtualbox:/home/pavan/K8S# kubectl get nodes
NAME                        STATUS   ROLES           AGE   VERSION
pavan-multi-control-plane   Ready    control-plane   15h   v1.29.2
pavan-multi-worker          Ready    <none>          15h   v1.29.2
pavan-multi-worker2         Ready    <none>          15h   v1.29.2
root@pavan-virtualbox:/home/pavan/K8S#
```
- Here we can see that scheular scheduled in the node pavan-multi-worker and pavan-multi-workder2.

### NOTE : USUALLY I USE NANO EDITOR INSTEAD OF VIM

```
1. Open a terminal window.

2. Type the following command and press Enter:
   ```
   export EDITOR=nano
   ```

3. This command sets the `EDITOR` environment variable to nano for the current session. To make this change permanent, add this line to your shell configuration file (like `~/.bashrc` or `~/.bash_profile`). You can do this by opening the file in a text editor and adding the line `export EDITOR=nano` to the end of it.

4. Now, when you run `kubectl edit nice pavan-multi-worker`, it will open the resource in nano instead of vim.

5. To verify that nano is being used as the default editor, you can run `echo $EDITOR` in the terminal. It should output `nano`.

With these steps, you've successfully configured kubectl to use nano as the default editor.
```
- NOW WE ARE PROVIDING THE SAME NAME AS WORKER-NODE HAS by editing this node ``` kubectl edit node pavan-multi-worker ```
```
labels:
    beta.kubernetes.io/arch: amd64
    beta.kubernetes.io/os: linux
    kubernetes.io/arch: amd64
    kubernetes.io/hostname: pavan-multi-worker
    kubernetes.io/os: linux
    node-name : arm-worker
  name: pavan-multi-worker
  resourceVersion: "31273"
  uid: 5e29b1ff-dc40-415c-996a-f0e1950703ae
```
- IN THE DEPLOYMENT YAML FILE THE NODE AFFINITY IS
```
spec:
      affinity:
       nodeAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
          preference:
           matchExpressions:
           - key: node-name
             operator: In
             values:
             - arm-worker
```
- NOW IF WE APPLY IT WILL USE THE SAME NODE, BUT PERVIOUSLY WE DON'T HAVE EXACT MATCH OF THE NODE SO IT WAS SCHEDULED ON ANY AVAILABLE NODE.
```
root@pavan-virtualbox:/home/pavan/K8S# kubectl apply -f deployment-preferred-sched.yaml 
deployment.apps/nginx-deployment created
root@pavan-virtualbox:/home/pavan/K8S# kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP           NODE                 NOMINATED NODE   READINESS GATES
nginx-deployment-76d94ccc75-qbvjv   1/1     Running   0          9s    10.244.2.5   pavan-multi-worker   <none>           <none>
nginx-deployment-76d94ccc75-t6dd7   1/1     Running   0          9s    10.244.2.4   pavan-multi-worker   <none>           <none>
nginx-deployment-76d94ccc75-x6b55   1/1     Running   0          9s    10.244.2.6   pavan-multi-worker   <none>           <none>
root@pavan-virtualbox:/home/pavan/K8S#\
```

- REQUIRED WORKS SAME AS NODE SELECTOR
```
 spec:
      affinity:
       nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
         nodeSelectorTerms:
         - matchExpressions:
           - key: node-name
             operator: In
             values:
             - arm-worker
```  


- 
3. Taints

- WE HAVE A KUBERNETES NODE, WE DON'T WANT TO SCHEDULE ANYTHING ON THIS NODE
- ANY POD SHOULD NOT TO SCHEDULED ON THIS PARTICULAR NODE.
- HERE EXACTLY THIS SITUATION COMES IS WHEN WE WANT TO UPGRADE
- LET'S SAY FOR EXAMPLE WE HAVE A PROD KUBERNETES CLUSTER AND WE WANT TO UPGRADE IT TO NEW VERSION LET'S SAY 1.29
- SINCE THIS IS PRODUCTION KUBERNETES CLUSTER WE CAN NOT UPGRADE ALL THE NODES AT A TIME
- WE WILL DRAIN A PARTIICULAR NODE WHERE WE WILL MOVE ALL THE PODS THAT ARE THERE IN THAT PARTICULAR NODE TO A  DIFFERENT NODE, ONCE THEY MOVE TO DIFFERENT NODE THEN WE WILL MAKE THE NODE UNSCHEDULED.
- ONCE IT IS UNSCHEDULED - THIS NODE WILL BE IDLE KUBE SCHEDULER WILL NOT GOING TO SCHEDULE ANYTHING ON THIS PARTICULAR NODE
- SO WE CAN BRING THIS NODE DOWN AND WE CAN UPGRADE IT, AND WE REMOVE THE UNSCHEDULABLE STATUS AND THE NEW VERSION WILL BE UP AND RUNNING
- THERE ARE MULTIPLE WAYS TO UPGRADE KUBERENETES CLUSER THIS IS THE ONE WAY
- FOR THIS WE USE A CONECPT CALLED TAINT
- WE CAN THINK TAINT AS A LABEL - WHICH EXPLAIN TO THE KUBE SCHEDULER THAT MY NODE HAS BEEN UNSTABLE 

**Taints are applied to nodes to repel certain pods. They allow nodes to refuse pods unless the pods have a matching toleration.
Usage: Use kubectl taint command to apply taints to nodes. Include tolerations field in the pod's YAML definition to tolerate specific taints.**

```
kubectl taint nodes node1 disktype=ssd:NoSchedule
```

```
spec:
    containers:
    - name: my-app
    image: my-image
    tolerations:
    - key: disktype
      operator: Equal
      value: ssd
      effect: NoSchedule
```

4. Tolerations

Tolerations are applied to pods and allow them to schedule onto nodes with matching taints. They override the effect of taints.

Usage: Include tolerations field in the pod's YAML definition to specify which taints the pod tolerates.

```
spec:
  containers:
  - name: my-app
    image: my-image
  tolerations:
  - key: disktype
    operator: Equal
    value: ssd
    effect: NoSchedule
```
