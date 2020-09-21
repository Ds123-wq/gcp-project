# gcp-project

Overview
1. Create multiple projects namely developer and production

2. Create VPC network for both the projects

3. Create a link between both the VPC networks using VPC Peering

4. Create a Kubernetes Cluster in developer project and launch any web application with the Load balancer

5. Create a SQL server in the production project and create a database

6. Connect the SQL database to the web application launched in the Kubernetes cluster

Step-1 Create two different project.
A project is really an organizational construct used for billing and permissions.Google  organize resources from a billing and permissions perspective, and each project has its own VPC network(s) isolated from other projects. So to get separate environment ,First I create two project :- Dev-project and Production-project

Dev-testing-project

No alt text provided for this image
Production-project

No alt text provided for this image
Step-2 Create a VPC for dev-project
“A Virtual Private Cloud (VPC) is a global private isolated virtual network partition that provides managed networking functionality for your Google Cloud Platform (GCP) resources.”

No alt text provided for this image
Here I create a vpc in dev-testing project with name mydev-vpc. I also create a subnet inside this vpc.

No alt text provided for this image
Here I give a IP range to vpc .So in this vpc when we launch any instance ,they get ip in this ip range. and select a region asia-southeast1. So My VPC launch in this region.

No alt text provided for this image
After create a vpc and subnet .I make this subnet public.So our client can connect to our wordpress site from anywhere.

Now I create firewall for dev-vpc .So our wordpress site accessible by client.

No alt text provided for this image


No alt text provided for this image
In this firewall ,I allow all and make this vpc as a public vpc. So everyone can access my wordpress site.

No alt text provided for this image
Step-3 Create VPC for production -project
Now I create a vpc in the production project.

No alt text provided for this image
Here I select us-east1 region and gives a different ip range to the dev-vpc .SO in future ip range would not conflict .

No alt text provided for this image
Add firewalld rule to prod-vpc

No alt text provided for this image


Now publically both vpc has connectivity.But I don't trust on the public network.Because it slow and not secure.So I want to use Google private network to connect our mysql database to wordpress. For this VPC-peering is used.

Step-4 VPC peering between dev-vpc and prod-vpc
Google Cloud VPC Network Peering allows internal IP address connectivity across two Virtual Private Cloud (VPC) networks regardless of whether they belong to the same project or the same organization.

VPC Network Peering enables you to connect VPC networks so that workloads in different VPC networks can communicate internally. Traffic stays within Google’s network and doesn’t traverse the public internet.

So I create a vpc-peering in dev-testing project

Dev-peering

No alt text provided for this image


No alt text provided for this image
No alt text provided for this image
As we see the status of dev-peering is inactive. Because other side also allow for the peering. So I also create a vpc-peering in production project

No alt text provided for this image
Prod-peering

No alt text provided for this image


As soon as I create a vpc-peering in production ,both vpc-peering become active.Now we use Google private network.

No alt text provided for this image
Step-5 Launch k8s Cluster in Dev-testing project
Now I launch a Kubernetes cluster in the dev-testing project.

No alt text provided for this image


No alt text provided for this image


No alt text provided for this image
I launch k8s cluster in asia-southeast1 region . I launch this cluster in all subnet .So in future ,if any natural disaster comes ,it would not affect our cluster.

No alt text provided for this image
I select one node .So in each subnet one-one node is launched.So finally we got three nodes in this cluster.

No alt text provided for this image


No alt text provided for this image


No alt text provided for this image
Now we have successfully launch a cluster with three nodes in our created vpc in dev-testing project.

Step-6 Launch mysql database in production
Now we will create MySQL Database using GCP SQL service.

No alt text provided for this image
After launch mysql database, we have to add rule .So we can connect to database.For this we got to Edit configuration and go to connectivity section and add network .

No alt text provided for this image


No alt text provided for this image
No alt text provided for this image



Step-6 Launch wordpress/frontend of website
First We will install Google SDK. Download and Install it. After Installation, it will ask us to choose a Google Account. After selecting the account we will be authenticated by Google Cloud SDK. Now we can able to contact GCP from our cmd of window.

After launch k8s cluster , we need k8s client program to contact to the k8s cluster .So we download kubectl program from internet and install in laptop. After install ,we can verify by command

kubectl.exe
No alt text provided for this image
After this, kubectl program require three thing to connect k8s cluster of GCP

URL of k8s cluster or cluster end point
username
password
All three things we get by run a command that provided by them

No alt text provided for this image
When we run command

No alt text provided for this image
We see our k8s cluster slave nodes

No alt text provided for this image


Now create deployment to launch wordpress site by the command

kubectl.exe create deployment mywp --image=wordpress 
No alt text provided for this image
Use load balancer of GCP to manage load

kubectl.exe expose deployment mywp --type=LoadBalancer --port=80 
No alt text provided for this image
We see a load balancer is launch in GCP cloud

No alt text provided for this image


Step-6 Connect wordpress and mysql database
Now we can connect to wordpress by external ip of load balancer.When we paste this ip in browser, we get welcome page of wordpress

No alt text provided for this image
No alt text provided for this image
No alt text provided for this image
If our wordpress and mysql have connectivity,then we see a screen to install wordpress

No alt text provided for this image
No alt text provided for this image
Now we create our site and set username and password. and write blogs and publish on it.

No alt text provided for this image


Now we see wordpress and mysql have connectivity.So when we write blog on the wordpress site ,it will store in mysql database.

Thanks for reading...

