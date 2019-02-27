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

Q - If you would like to control Load Balancing of an Web App, would we go for IAAS, PAAS or SAAS
This is a typical IAAS scenario, which is Google Compute Engine in this case. We can setup OS, Select Hardware. From Configuration to management all is on us. This is like setting up an on-premise system, the only difference being absolutely no need for buying the hardware or installing os/languages. This is required we want to control the load balancing and scaling for our Web Application.
