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
1ST SENATIO - IMAGE INVALID, IMAGE DON'T EXISTS
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

2ND SENARIO - SECRET ISSUE - PRIVATE REGISTRY 
--

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/cdd36ab6-0d35-4766-975a-e7e367458f32)

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/cd216393-6370-44e5-a201-b7f59cc1683b)

REF : https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/53dc77b8-25ff-4758-8aba-391aa68aee21)

TO pull private image 
--
TO DOCKERHUB : ``` kubectl create secret docker-registry demo --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>          ```
TO AWS ECR : ``` kubectl create secret docker-registry  \
  --docker-server=${AWS_ACCOUNT}.dkr.ecr.${AWS_REGION}.amazonaws.com \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password) \
  --namespace=default
```

- We need to store private repo docker credentials in kubernetes secrets
- Above command will create a secret
- To get seccrets we need to use ``` kubectl get secrets ```

![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/771efc8b-9397-4785-a3d7-5c23dfc057bf)

- ![image](https://github.com/pavankumar0077/kubernetes-troubleshooting-zero-to-hero/assets/40380941/1af7f9ad-8924-4d53-9931-6f15bc5538b4)
- With secret

```

For ECR
--
REF LINK : https://docs.aws.amazon.com/AmazonECR/latest/userguide/docker-pull-ecr-image.html
