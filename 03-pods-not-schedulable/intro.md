## USE KIND TO WORK WITH MULTI NODE CLUSTER

### INSTALLATION 
- ``` https://kind.sigs.k8s.io/docs/user/quick-start/#installation ```
- Switch to root if you are facing any errorss

### Create a cluster
- ``` kind create clusterr --name=pavan ```
- ``` kind get clusters ````

### Create a multi node cluster
- ``` ind create cluster --name pavan-multi --config=multi-node-cluster.yaml ```

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```
- This is the yaml file Here, we can inccrease WORKER NODES AND CONTROL-PLANES AS WE NEED.

### Delete a cluster
- ``` kind delete cluster --name pavan-multi ```

### IF K8S is pointing to pervious cluster that you have deleted
### TO get the all clusters that are configured with kube config 
- ``` kubectl config view | grep kind ```
- or
- ``` kubectl config view ```

- ``` kubectl config use-context kind-multi ```
- The above command will help us to switch the context, KUBE CONFIG might be pointing to multiple kubernetes cluster
- When we working in organization we might need to work with different env's like testing clusters, development clusters 
