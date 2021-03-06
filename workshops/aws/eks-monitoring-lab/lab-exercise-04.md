# Lab Exercise 4
## Deploy AD-Capital Application to EKS on the LaunchPad Server



In this exercise you will need to do the following:

- Connect to your LaunchPad Server via SSH
- Validate that your EKS cluster has deployed properly
- Run a script to create a service account in your EKS cluster
- Create an API Key using the AppDynamics Controller
- Run a script to set environment variables for EKS deployment
- Run a script to deploy the AD-Capital application to EKS
- Monitor the results of the deployment in the AppDynamics Controller

<br>

### **1.** Validate Your EKS Cluster
ssh into the launchpad Server

```
ssh -i AppD-Cloud-Kickstart-AWS.pem centos@FQDN_OF_MACHINE
```

Using the SSH terminal for the Launch Pad EC2 instance, run the commands below to validate your EKS cluster creation is complete and running:

```
cd /home/centos/AppD-Cloud-Kickstart/applications/aws/AD-Capital-Kube

kubectl get nodes

kubectl get namespace
```
You should see output from the commands similar to the image seen below:

![EKS Cluster Validation](./images/4.png)

<br>

### **2.** Create a Service Account in EKS Cluster
Now run the commands below to create a service account in your EKS cluster:

```
cd /home/centos/AppD-Cloud-Kickstart/applications/aws/AD-Capital-Kube

./create_eks_service_account.sh
```
You should see output from the commands similar to the image seen below:

![EKS Service Account](./images/5.png)

<br>

### **3.** Create an API Key using AppDynamics Controller
AppDynamics analytics engine provides a mechanism to communicate with 3rd Party data consumer or producer through REST API. The Analytics Events REST API provides Publish Events endpoints to receive events. We need to configure the API to receive events from Kubernetes Agent. API Keys provide a secure authentication mechanism for a caller to prove identity when using these public REST APIs.

Open your web browser and login to your controller.  The URL you need to use is like the one below.  You can find your controller's public IP address or public host name in the AWS EC2 console.

*http://controller-ip-or-hostname:8090/controller/*

You should see the controller's login screen as seen in the image below.  The default user name and password are listed below. Login into the controller.

- Username: admin
- Password: welcome1

![Controller Login](./images/6.png)

<br>

Once logged into the controller, navigate to the Analytics module and create the API key using the steps below:

1. Click on the Analytics tab on the top menu
2. Click on the Configuration tab on the left menu
3. Click on the API Keys tab on the middle menu
4. Click the Add button with the **+** symbol
5. Fill in the name, description, and check the 3 boxes seen in the image below
6. Click the Create button


![Analytics API Key 1](./images/7.png)

<br>
After you click the Create button you will see the next screen below.  Copy the API key and save it in a text file for the next step.

<br>

![Analytics API Key 2](./images/8.png)

<br>

### **4.** Set Environment Variables for EKS Deployment
Run the command below to set the variable for the controller host, replacing 'your-controller-host-name-or-ip' with the public IP address or public host name of your controller:

```
export appd_controller_host=your-controller-host-name-or-ip
```

Now run the next command below to set the variable for your API key, replacing 'your-api-key' with the API key you just created:

```
export appd_es_api_key=your-api-key
```
Run the commands below to complete the process of setting all the variables needed to deploy to the EKS cluster:

```
cd /home/centos/AppD-Cloud-Kickstart/applications/aws/AD-Capital-Kube

./create_env_configmap.sh
```
You should then see output similar to the image seen below:

![EKS Configmap](./images/9.png)

**NOTE:** If your application does not show up in AppD, there was likely an issue with the IP / FQDN passed along or the config map. You will need to delete your cluster and recreate after verifying those steps.  

The command to delete your cluster and rerun after variables have been reviewed is:

```
kubectl delete -f Kubernetes/
```

<br>

### **5.** Deploy the AD-Capital Application to EKS

To deploy the AD-Capital application to the EKS cluster, run the commands below:

```
cd /home/centos/AD-Capital-Kube

kubectl create -f Kubernetes/
```
You should then see output similar to the image seen below:

![AD-Capital Deploy](./images/10.png)

Now wait four minutes and run the command below to validate that the EKS pods are running:

```
kubectl get pods -n default
```
You should then see output similar to the image seen below:

![EKS Pods](./images/11.png)


<br>

### **6.** Monitor Deployment in the AppDynamics Controller

Wait four more minutes and go to your web browser and check the controller to see if the AD-Capital application is reporting to the controller.  You should see what the image below shows when you click on the Applications tab:

![Controller Apps](./images/12.png)

Wait a few more minutes and you should see the flow map that looks like the image below:

![Flow Map](./images/13.png)

<br>

[Overview](aws-eks-monitoring.md) | [1](lab-exercise-01.md), [2](lab-exercise-02.md), [3](lab-exercise-03.md), 4, [5](lab-exercise-05.md), [6](lab-exercise-06.md) | [Back](lab-exercise-03.md) | [Next](lab-exercise-05.md)
