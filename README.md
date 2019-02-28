# GCP_Notes

Gcloud Commands-
1. gcloud auth list
2. gcloud config list project
3. gcloud -h
4. gcloud config --help
5. gcloud help config
6. gcloud config list
  What properties have been set for the configuration
  Only shows those configs that are different from the default
7. gcloud config list --all
  To see all the the configs including the deaults
  The properties that shows (unset) are those which have their default values or that are not set at all
8. export PS1 = '$'
  Change how the terminal window looks like

Q - Simplest way to host a website in a plaform like GCP

Google offers 3 compute options
1. Google App Engine is PAAS option - serverless and opsfree.
2. Google Compute Engine is the IAAS option - fully controllable down to the OS.
3. Google Container Engine lies in between - cluster of machines running kubernetes and hosting containers

Case Study - 
Hosting a website - No SSL, No CDN, No HTTPS, no deployment help, nothing just a static html file
Then we use Google Cloud Storage. Cloud storage is static immutable storage option. It is cost effective. You can create Html/Css files and Upload the files to cloud bucket though Web console or a cloud shell. We can store the code in github and run a update script using webhook. We can also use CI/CD tool like jenkins and upload it to cloud storage. Jenkins has a cloud storage plugin for post-built step. We create cloud buckets to store static or user uploaded media content. It also provides a domain name, which can be modifed by the user

Hosting a website - SSL, CDN, HTTPS Serving, Still Quite static, but rich
Firebase Hosting + Google Cloud Storage - Provides SSL so that HTTPS Serving is possible, ~~CDN~~ Edges over the World, and ~~Atomic Deployment~~ and ~~One click rollback~~. Basically a Google Storage ++
It is still pretty basic and we are yet to have a fully functional web app

# Google Cloud Engine
Q - If you would like to control Load Balancing of an Web App, would we go for IAAS, PAAS or SAAS
This is a typical IAAS scenario, which is Google Compute Engine in this case. We can setup OS, Select Hardware. From Configuration to management all is on us. This is like setting up an on-premise system, the only difference being absolutely no need for buying the hardware or installing os/languages. This is required we want to control the load balancing and scaling for our Web Application.

GCE - Google Cloud Launcher
Lamp Stack or Wordpress in a few minutes.
Cost Estimates before deployment
Select everything from hardware to disk space
can customize the fully control the instances after deployment

Storage Options-
1.Cloud Storage Buckets
2.Standard Persistent Disks
3.SSD
4.Local SSD
5.Cloud SQL - Postgre SQL or MySQL
6.NoSQL - Hive or HBase

Load Balancing - 
Network Load Balancing - Upper level in OSI Stack. Less sophisticated.
HTTP Load Balancing - Deep in the OSI Stack. More sophisticated
Internal Load Balancing can be done on an private network, and this can be autoscaled too

Dev Ops -All 3rd party tools have Gcloud equivalents. There are some gcloud alternatives to most of these 3rd party tools.
They abstract away most of the devops stuff. Shift to containers or app engine instances.

There are Load Balancing in Plaform as a Service - Like ~~Heroku~~. But we won't have fine grain tuning.
So it's better to use the Infra as a Service

# Creating a Virtual Machine Instance
Q - What factors would you like to take into account when you instantiate a compute engine VM

## Zones
1. They are geographical locations
2. An instace is billed differently based on the zone it is situated in
3. If most of your traffic comes from South East Asia, you won't want your request to go all the way to America
4. Also if you are a part of a Govt organisation, there may be a security concern placing the data in a different country
5. Some zones are more expensive than the others, based on the resource availabilty

Factors to consider while you instantiate a VM - OS, Hard Disk, Processor based on Computation that needs to be performed on it. Zones and Https/~~access to APIs/SSH~~

# More GCE
Q - If you do decide to use a pre-emptible VM, what precautions would you take?
Pre-emptible Vm are the VMs that can be shut down by the GCP at any time within a 30 seconds notice.
They are available with fraction of the cost of the regular machine types.
Our program must be fault tolerant and mostly we should have a shutdown scipt that must run within 30 seconds and that too gracefully. Is shut down atleast once in 24hrs. Probability of termincation depends on Zone/Availability/Day/Maintenance/Migration

## Preemptible Instance - How it works
Step 1 - Sends a soft off signal
Step 2 - The machine has 30 seconds to run the shutdown script and cleanup gracefully and give up control.
Step 3 - If not, the compute engine gives a mechanical shutdown signal and the machine is transitions to terminated state
We must have a well written shutdown script and we have to assossiate the vm instance to this shutdown script using console or any other administrative option.
Do specify whatever cleanup operation you would require in the shutdown script and make sure that it runs gracefully within 30 secs
## Storage Options
Every VM comes with small root disk. We can select additional Storage from menu.
1. Persistent Disks - Hard Disk and SSD / Are redundant
2. Local SSD - Non redundant but fast
3. Cloud Storage - Cheap blob storage

GCE Uses stackdriver for logging and monitoring

Choices that we need to take care before spinning up a VM instance
1. OS - Linux/Windows/Any custom OS that you have written
2. In virtual machine instance, we have full root access and ssh capability
3. Zone - Zone affects billing
4. Machine Types - Standard, High-Mem, High-CPU, Shared-core ( for small, resource intensive tasks ). We can attach GPU dies to most machine types

Each VM Instance must belong to a project. A project can have any number of instances.
A project can have 5 Virtual Private Cloud.
Instances within a VPC communicate using lan, Instances accross VPC communicate using the Internet - VPN.
A VM Instance belongs to exactly one of these VPCs.

Q - What would you do if google shuts down your instance for Maintenance.
# Editing a VM Instance
Compute Instances - 

1. Setting to delete the boot disk once the instance is deleted
2. Setting to convert it to Pre emptible
3. Setting to automatically restart, that is, if the vm is terminated, it will automatically restart
4. If the instance is brought down by google for maintenance, then we can choose to migrate the VM to another machine. This is the recommended setting. We can shutdown altogether if we don't want to migrate.
5. Service account - A common account. Will study in much more detail

Setting - Stop the Vm. Change the machine type, and start the instance.
Note: We can't change the zone once the instance has been created
We can add additional hard disk for use.
We can set up the access for each of our Cloud APIs. We can give the access to all the APIs or more granularly select the APIs that we want to access though out VM Instance.

Q - How would you spin up multiple VMs repeatedly.
A - We can use the command line console provided by google to create automated scipts. The command can be copied from the google console, once we have configured the VM Instance just the way we want to.

Setting while spinning up a VM  - 
Labels (Under the management section is the create VM Instance Page under Google Compute Engine) - We can add labels to logically group some resources. In the billing page we can see the billing details/usage patterns of those resources associated with the specific label.

We can get an equivalent gcloud command to spin up a VM with the exact same configuration as specified in the Console.
This way we can spin up multiple customized VMs repeatedly.

## Command to create instance
###### create a new instance
gcloud compute instances create another-instance --zone us-central1-a
###### help for quick lookup
gcloud compute instances create --help
###### change zone
gcloud config set compute/zone us-central1-c
All new instances will be created in this zone

If we now try to SSH a VM that is currently not on the default zone the ssh will fail. Thus we would need to change the default zone parameter, or specify the zone in the ssh command.

###### ssh to a vm instance
gcloud compute ssh <vm-name>
this instance must be in the same zone as the default zone set in the console.
  
Q - What is the necessary conditions before I attach a persistent disk to my VM, necessary condition under which a persistent disk has to be created before it can be attached to a VM
A - They need to be in the same zone
###### Create a Disk
gcloud compute disks create test-disk --size=100GB --zone us-central1-f
We need to make sure that the persisten disk resides in the same zone as the VM. We cannot connect a VM with a persistent disk that resides in a completely different zone. The IO requirement necessary won't be possible if the VM Instance and the Persistent Disks remain in completely different zone. Make the disk atleast 200 GB for optmial read/write speeds

We need to format this disk before use.

We can add this Newly created Disk in the settings of the VM Console. Just select the disk under additional storage menu.

Also, we can use the gcloud command line tool
gcloud compute instances attach-disk test-instance --disk test-disk --zone us-central1-f

We can go into the setting page, to see whether the disk has really been attached.

To get all the hardisk associated with the specific vm, run
ls -l /dev/disk/by-id/
# Google Kubernetes
Q - A VM is more lightweight than a docker container? True or False

GCE - Infra as a Service

Definition of a container - A container is a lightweight executable package of a piece of software which contains everything the code needs to run, for eg - libraries, system files etc.

So a container has it's own little env isolated from one another. It doesn't have a guest os. Unlike VM, which has a guest OS, with a virtualisation monitor such as Hypervisor.

Docker Containers -> Docker -> Host OS -> Infra
VM Image -> Guest OS -> Hypervisor -> Infra

Docker containers are lightweight VMs
Containers can run in a cluster mode. A master node which runs Kubernetes, monitors the worker nodes, called Node Instance. The worker nodes communicate with the master node known through Kubelet, and also run a bunch of containers. The node instances has a kubelet and a pod. Inside each pod runs a bunch of containers

So VM has a full OS, which communicates with the Hypervisor, but in case of Container, we virtualise the OS, the Docker acts as a proxy between container and the OS

Advantages - 
Componentization/Micro-Service
Portability
Rapid Deployment

A bunch of containers can be encapsulated using a container registry - Google Container Registry

Reduction of Orchestration - Using config files
Regstry mechanism - pull and pish image to the registry
App Engine can seamless interact with Containers and App Engine VMs

# More GKE
Q - Scaling a container engine is hard work. only use app-engine for auto scaling. True or False?

We had persistent disks in VM. Each VM comes with its persistent disk that is attached to it as long as the instance is present. Even if we detach the disk and attach it with a different instance, the data is going the be there. But we have something different in case of containers. We have a type of attached disk, that has ephemeral storage, i.e. the data is lost when the container restarts.

To have a persistent storage, we have an abrstraction of a Persisten Storage in the form of GcePersistentDisk. To have the data non-ephemeral.

Network balancing works out of the box, but HTTP load balancing is difficult to achieve. We need to do interfaces of our own with Compute Engine Load Balancing Infrastructure

GCE - Compute
GKE - Kubernetes
Kubernetes - > Kubelets - > Individual Containers
-> Means 'inside those'

Kubernets is the orchestrator. It controls the Kubelets which in trun control the pods. The pods share the same resources and groups a bunch of containers. Pod may have the same ip address, or share disk volumes. Configured by json or yaml.

So the instances are the a group of VM instances each of which are running kubernetes

