# ImagePullBackoff

When a kubelet starts creating containers for a Pod using a container runtime, it might be possible the container is in Waiting state because of ImagePullBackOff.

The status ImagePullBackOff means that a container could not start because Kubernetes could not pull a container image for reasons such as 

- Invalid image name or 
- Pulling from a private registry without imagePullSecret. 

The BackOff part indicates that Kubernetes will keep trying to pull the image, with an increasing back-off delay.

Kubernetes raises the delay between each attempt until it reaches a compiled-in limit, which is 300 seconds (5 minutes).

shh
My Notes

REF : ``` https://kubernetes.io/docs/reference/kubectl/quick-reference/ ```

--
Pulling a container image
--
IMAGE PULL
--
![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/49c34360-ef0d-4994-a35a-12798cada965)

1. Invalid Image name
2. Non- existent (Image is deleted)
3. Private image pulling (Without ImagePullSecret) - This should have a docker credentials as a secret to pull private docker iamge.
4. If we want to pull an iamge from ECR process is same like for AWS OR AZURE          

To Find which error caused issue
--
1. Use kubectl describe command
2. Use kubectl events

BACKOFF
--

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/be11c8fb-3862-418e-8c42-0c9ae284ab1b)

1. Kubernetes directly not provide IMAGE PULL BACK OFF ERROR directly, First it will throw ERROR IMAGE PULL error
2. It will wati for sometime (Becoz of may be network issues any other issues -- it will wati and try again)
3. It will continously wait and try and wait and try like that.
4. Finally ErrorPullImage will converted into ImagePullBackOff error.

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/d34046a8-0af7-453f-bf8f-5aa7c4ccf949)

- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/359d9135-7eed-42ae-acc5-912a29b711e5)
- In the container selection if we want to point to to ECR OR ACR we need to mention CONTAINER REGISTRY LIKE ECR REGISTRY NAME, 
- If we don't mention anything then it will like docker.io/nginx:1.8.4 like this followed by image. ( IMAGE REGISTRY - FOLLOWED BY IMAGE )

ERROR 
--
- FIRST ERRORIMAGEPULL
- NEXT IMAGEPULL BACKOFF
- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/e70fe044-f020-48a7-8ef6-6e0b11413af6)

2ND 
