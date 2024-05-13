## STATEFUL IS WORKING FINE IN AWS EKS, BUT NOT WORKING ON ANY OF THE OTHER CLOUDS LIKE AKS, GCP, MINIKUBE AND etc.

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/0fb98d1b-1560-4649-88ac-1d823fa49388)

## TO GET STATEFULSET
``` kubectl get statefulset ```

## ISSUE
```
root@pavan:/home/pavan/Pavan-Learnings# kubectl apply -f sample-statefulset.yaml
statefulset.apps/web created

root@pavan:/home/pavan/Pavan-Learnings# kubectl get statefulset
NAME   READY   AGE
web    0/3     14s

root@pavan:/home/pavan/Pavan-Learnings# kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
web-0   0/1     Pending   0          106s

```
- Here the issue is we are expecting 3 pods, but here it is one and that too in the pending state.

```
root@pavan:/home/pavan/Pavan-Learnings# kubectl describe pod web-0

Warning  FailedScheduling  2m24s  default-scheduler  0/1 nodes are available: pod has unbound immediate PersistentVolumeClaims. preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling
```
- Here we are trying to deploy a pod through statefulset, but the pod that you are trying to deployo is requesting for a PERSISTENT VOLUME - PV, But it is not finding the persistent volume, BUt we  have 3 replicas.
- EVEN WE HAVE 3 REPLICA'S THE SECOND REPLICA WILL BE CREATED ONLY ONCE THE 1ST REPLICA IS CREATED SUCCESSFULLY, BUT IN THE DEPLOYMENT IT IS LIKE ALL THE 3 WILL BE CREATED AT ONCE.
- HERE 1ST POD IS IN PENDING STATUS SO THE 2ND POD EVEN NOT SCHDULED
- THAT IS THE REASON WE ARE SEEING ONLY ONE POD

## WORKFLOW OF THE STATEFUL SET
- When we create stateful set and request for a persistent volume, In the stateful set definiation as a developer will mention a PERSISTENT VOLUME CLAIM
- A CLAIM REQEUST THAT THIS STATEFUL SET IS MAKING
- WHERE IN THE CLAIM REQUEST IT IS ASKING FOR 1 GB OF PV volume
- Depending on the PVC, Usually every PV will have a storage class -- THis stroage class using a PROVISIONER -- creates a PV 
- STATEFUL SET -- PVC -- STROAGE CLASS -- PROVISIONER -- PV

## FROM THE PROBLEM STATEMENT - STATEFULSET IS WORKING FINE IN AWS (EKS)
- WHEN THE DEVELOPER HAS CREATED STATEFULSET ON THE AWS EKS PLATFORM
- DEVELOPER HAS MENTIONED THE STROAGE CLASS AS EBS IN THE YAML FILE
```
volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: ebs
      resources:
        requests:
          storage: 1Gi
```
- HERE THE PVC MENTIONED IS EBS WHICH MEANS 
