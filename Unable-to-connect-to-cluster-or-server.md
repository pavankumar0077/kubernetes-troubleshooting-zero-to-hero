## UNABLE TO CONNECT TO THE CLUSTER/SERVER

![image](https://github.com/user-attachments/assets/3df53b02-599f-442f-bd08-e76b81221a1a)

### Troubleshooting steps
1. Check the kubeconfig or config file - Ensure that kubeconfig file containers correct API SERVER ENDPOINTS, And verify that the server field in the kubeconfig file match the EKS ENDPOINT
2. Verify network connectivity - Test your network conntivity to the API SERVER ENDPOINT, To check us CURL K endpoint URL, This will ensure that no firewell rules or security groups are blocking the access the endpoint\
3. Validation the IAM ROLE PERMISSIONS - IAM role or the user that we are using has the necessary permissions to interact with the EKS, attach some of the ppolicies or roles like amazon eks cluster policies like that with ec2 instance that we are using as a client, amazon eks workder node policies
4. Refresh Authentication Token - We can generate new token to not get connection issues we have commend as well we can use AWS EKS GET TOKEN CLUSTER NAME -- REASON
5. Check cluster status - aws eks describe cluster
6. Verify AWS CLI Configurations - Ensure that aws cli is correctly configured and has valid credentials, Verify correct AWS PROFILE is been used if we have multiple profiles  configured
7. DNS Resolution - Use any dns propagation tools like nsloop up or dig we can use command like dig cluster-endpoint
8. Check VPC configuration - VPC and subnet are configured correct with the eks cluster and check whether it has proper routing tables and security groups 


