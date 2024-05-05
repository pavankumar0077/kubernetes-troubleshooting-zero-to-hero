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

3. Taints

Taints are applied to nodes to repel certain pods. They allow nodes to refuse pods unless the pods have a matching toleration.
Usage: Use kubectl taint command to apply taints to nodes. Include tolerations field in the pod's YAML definition to tolerate specific taints.

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
