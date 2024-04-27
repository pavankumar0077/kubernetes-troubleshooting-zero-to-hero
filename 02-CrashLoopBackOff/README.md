# CrashLoopBackOff

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/a0961c84-6ae3-47c4-b052-f091e8f9fdeb)

When you see "CrashLoopBackOff," it means that kubelet is trying to run the container, but it keeps failing and crashing. After crashing, Kubernetes tries to restart the container automatically, but if the container keeps failing repeatedly, you end up in a loop of crashes and restarts, thus the term "CrashLoopBackOff." 

This situation indicates that something is wrong with the application or the configuration that needs to be fixed.

## Common Situations of CrashLoopBackOff

The CrashLoopBackOff error in Kubernetes indicates that a container is repeatedly crashing and restarting. Here are explanations of how the CrashLoopBackOff error can occur due to the specific reasons you listed:

### Misconfigurations

Misconfigurations can encompass a wide range of issues, from incorrect environment variables to improper setup of service ports or volumes. These misconfigurations can prevent the application from starting correctly, leading to crashes. For example, if an application expects a certain environment variable to connect to a database and that variable is not set or is incorrect, the application might crash as it cannot establish a database connection.

### Errors in the Liveness Probes

- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/2ed445b7-c984-480c-908c-348eb4b127c1)
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/0a5eeb3c-1039-4ad2-b858-7f6ad442755c)
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/ced96630-7bde-4272-a687-81b539eb8fa3)
- Here Liveness is failing be'coz we don't have that endpoint or health check present.
## NOTE : Liveness probe will be check for pod's health, and Readiness probe will check for the pod is ready to take traffic or not.

Liveness probes in Kubernetes are used to check the health of a container. If a liveness probe is incorrectly configured, it might falsely report that the container is unhealthy, causing Kubernetes to kill and restart the container repeatedly. For example, if the liveness probe checks a URL or port that the application does not expose or checks too soon before the application is ready, the container will be repeatedly terminated and restarted.

### The Memory Limits Are Too Low
K8s Cluster
==
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/ac336c61-75ad-4186-b083-b1f32d5b1595)
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/7cfc44f0-c660-4ecb-b145-7e5400af89f6)
- This is how kuberntes clusters is shared by different teams and apps in real time.
- In a cluster we have 3 nodes all together we have 60 CPUs and 64 GB ram, And 5 Namespaces
- We have 5 pods running and we didn't mention any cpu limit or limitations to consume for the pod.
- If First Namespace is taking 60 CPU and 64 gb of ram, As we are not mentioning the limitations,
- Then Second Namespace and rest NS the pods in these NS will fail be'coz of not CPU and RAM.
- We can limit the resouces on the NAMESPACE level - RESOURCE QUOTE
- We can limit the resouces on the Pod level as well - LIMIT RESOURCE
- Here it is CPU - 1000m and Memory - 1000Mi, Here CPU AND RAM ARE 1GB.
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/4391cc37-f925-4d76-a5a3-7e3b8c3673d6)
- This error is be'coz either RESOUCES ARE NOT ENOUGHT OR THE POD IS TAKING MORE RESOUCES THAN WHAT ACTUALLY IT SHOULD TAKE. IN ANY OF THESE CASES WE NEED TO ASK DEV's wHAT IS THE ISSUE WITHT THIS.
- As a DEVOPS ENGINEER we need to give the dev's like application logs pod logs and THREAD DUMP to dev so that they will analysis it and fix the resouces issue.


NOTE : DON'T INCREASE THE CPU OR MEMORY. IT IS NOT THE SOLUTION AND A VERY BAD PRACTICE. DEVs SHOULD IDENTIFY THE ROOT CAUSE ON WHY THE POD IS TAKING MORE RESOUCES THAN EXPECTED.
--
  
If the memory limits set for a container are too low, the application might exceed this limit, especially under load, leading to the container being killed by Kubernetes. This can happen repeatedly if the workload does not decrease, causing a cycle of crashing and restarting. Kubernetes uses these limits to ensure that containers do not consume all available resources on a node, which can affect other containers.
  
### Wrong Command Line Arguments

- Let's say for example we have an python based application, and we wrote dockerfile for it, In the file dockerfile we by mistake or typo in the CMD instead of app.py we wrote app1.py
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/bf53b263-1188-43b9-a1bc-09058ba8d551)
- Deployment apply -- Api server -- Node -- Kubectl will create container -- If no issues running state or if issues found Error state -- Then Crashed -- Kubernetes by default have restart policy it will restart the container again and this will be repated like backoff loop.
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/20aa7e51-de9b-419c-91a0-74b55e2fa911)
- Here we are see that Container is created -- Then went to Error state -- then Crashloop -- Again restart and -- same goes on.
- Now we have fixed the issue CMD where app name is different. After correction deployment is running
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/15909b36-5ae8-4d6d-9718-f41e88d7c4ae)


Containers might be configured to start with specific command-line arguments. If these arguments are wrong or lead to the application exiting (for example, passing an invalid option to a command), the container will exit immediately. Kubernetes will then attempt to restart it, leading to the CrashLoopBackOff status. An example would be passing a configuration file path that does not exist or is inaccessible.

### Bugs & Exceptions

Bugs in the application code, such as unhandled exceptions or segmentation faults, can cause the application to crash. For instance, if the application tries to access a null pointer or fails to catch and handle an exception correctly, it might terminate unexpectedly. Kubernetes, detecting the crash, will restart the container, but if the bug is triggered each time the application runs, this leads to a repetitive crash loop.
