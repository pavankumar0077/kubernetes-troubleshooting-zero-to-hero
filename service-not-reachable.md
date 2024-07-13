## SERVICE NOT REACHABLE

![image](https://github.com/user-attachments/assets/c9520dec-85c1-4f9b-8303-0c6b50083575)

![image](https://github.com/user-attachments/assets/05247d95-6fc6-4d6f-afef-513fb6607406)
- Issues are above left mentioned issues will cause service not reachable

![image](https://github.com/user-attachments/assets/a1b5fe71-bdb8-4263-a520-2fe9259dff36)
- Yellow colour - Deployment name
- Blue - ReplicaSet name
- Green - Pod name

# Debugging the service not reachable issue
![image](https://github.com/user-attachments/assets/15bdbf1c-24d6-41fd-a8bc-7e33c6577c6e)

- Here we can find that EXTERNAL-IP is SERVICE TYPE is LOAD BALANACER

# Describe the SERVICE
![image](https://github.com/user-attachments/assets/aaf56c58-444a-48a9-8cea-6413bc80fe95)
- Here we are clear see that we don't have ENDPOINT
- Verify deployment and services.
- ![image](https://github.com/user-attachments/assets/cb8be263-7a30-490d-8eb3-b1d8cca872fe)
- Deployment is fine, and running on default http 80 port and it is fine.
- ![image](https://github.com/user-attachments/assets/63b280ec-4476-4d3f-85d3-67890788de33)
- Here the problem is SERVICE IS NOT RECOGNIZED, SERVICE DON'T HAVE THE END POINT,
- IN THIS CASE SERVICE DON'T KNOW WHICH POD IT HAVE TO ROUTE THE TRAFFIC
- HERE THE ISSUE IS SELECTOR - MISMATCH, IT SHOULD BE MATCHED WITH LABEL OF DEPLOYMENT AND SERVICE SHOULD ALSO HAVE THE SAME LABEL
- WE HAVE TO MATCH WITH WITH MATCH LABELS, In the deployment we have nginx-app, but in the service we have nginx-service, so it should be also nginx-app
- WE DON'T HAVE ENDPOINT BE'COZ OF THIS ISSUE ONLY
- AFTER MODIFICATION IF WE APPLY SERVICE, THEN WE WILL SEE THE ENDPOINTS
- ![image](https://github.com/user-attachments/assets/75dcd767-02fd-4311-8506-bae513f1ea31)
- Now the application is working fine using ALB
- ![image](https://github.com/user-attachments/assets/cc5c4828-a64b-4f13-a334-b82853da0c04)
