## Example Voting App Kubernetes
Getting started with Amazon EKS – AWS Management Console and AWS CLI - Amazon EKS 
https://docs.aws.amazon.com/eks/latest/userguide/getting-started-console.html

First we need to create a IAM role for EKS service with default policy
![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/90b184a2-1ed3-4fcf-b924-7eb21b71eb58)

Next we have to create IAM user with EC2 admin access and take access/secret key from the user and configure CLI local terminal.

Create an IAM role for Cluster and Node with respective permissions.
MyAWSEKSCluster-role
![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/ac118789-9828-40ae-9293-368d56d882a6)
![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/cb63dd56-8620-4df0-969c-27b3cc7ff4fd)
![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/37e53344-cbb4-4338-b782-068656a33e55)

Create a Cluster and Node group with their respective roles attached to it

![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/ba36fbd1-4b95-4833-8ba7-e0bf707102f6)

Next we need to create an IAM user. An IAM user is needed to configure our AWS profile in local machine and perform all the actions on cluster & nodes from our local machine. I have attached Administrator access and some random EKS policies as i am still not completely aware of EKS policies will do further investigation and will attach appropriate policies.
Create and Note-down the access keys and secret keys of the user created.

Kubectl and AWS-CLI installation
Go through the below documentations for installations
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

Configure your AWS profile on your local machine with access & secret keys.

Now, we need to update kubeconfig using below command

aws eks update-kubeconfig --region us-east-1 --name Voting-app
We might get and error while pulling node info cluster info, make these changes in AWS cloudshell
![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/eae6dc7f-d740-4ce0-88e9-08b30634aa48)
Update and edit the kubeconfig file using the following command in AWS cloudshell 
![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/3043a90a-58b7-45ab-8d2c-57e23d4dc957)

In the below command add your respective Region and Cluster name properly.
aws eks update-kubeconfig --name eks-cluster-name --region aws-region
kubectl edit configmap aws-auth --namespace kube-system
And, Now edit the file with username ARN and username of the IAM user which you have created earlier
<img width="763" alt="image" src="https://github.com/PradeepMaheswaran/My-projects/assets/142527234/422fe59c-87b5-4a8e-a9e2-a3955ef240d3">

![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/d6476d8e-d484-4c83-b92b-50df3d2735af)
https://repost.aws/knowledge-center/eks-api-server-unauthorized-error

https://youtu.be/o9MJP9U4Nk8?si=owQEWSltCE2xSH0l

Download the Voting app yaml files from github and start creating pods with kubectl create command.
Create all the pods related to deployment and services.

![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/21db9c11-9d22-4ae0-9760-c4c240f394d8)
https://github.com/kodekloudhub/example-voting-app-kubernetes

kubectl create -f voting-app-service.yaml
kubectl create -f redis-deploy.yaml

We need to change the service type from nodeport to loadbalancer in  voting-app-service.yaml & result-app-service.yaml. This change is necessary as we are using cloud env for hosting our infra we need to mention “LoadBalancer” as the service type.

Also we need to remove the NodePort completely.

![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/40e05b22-69d0-40b1-ad39-cc93ee14945f)

Do this change before creation of pods.

![image](https://github.com/PradeepMaheswaran/My-projects/assets/142527234/27251de3-9e10-4dff-92f0-b2bd391fe6ad)

That’s it, now you will be able to access to application using the loadbalancer urls. Make sure ALL TCP is allowed in cluster security grp.

ac9be1480bdce47d48b57e972293e7ca-1393064416.ap-south-1.elb.amazonaws.com

You can use the DNS link which mentioned above or by using the below command
The below command will only work if we configure minikube
minikube service voting-service --url 
minikube service result-servcie --url


NOTE: If the voting application seems to be slow, your can create replicaset definition file and create multiple pods using replicasets
Eg: Replicaset yaml file,

<img width="727" alt="image" src="https://github.com/PradeepMaheswaran/My-projects/assets/142527234/d3964910-7be9-443f-b58b-b4a1854fd7ea">
