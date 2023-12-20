# Set Up and Configure a Cloud Environment in Google Cloud: Challenge Lab

### Challenge Scenario

As a cloud engineer in Jooli Inc. and recently trained with Google 
Cloud and Kubernetes you have been asked to help a new team (Griffin) 
set up their environment. The team has asked for your help and has done 
some work, but needs you to complete the work.

You are expected to have the skills and knowledge for these tasks so don’t expect step-by-step guides.

You need to complete the following tasks:

- Create a development VPC with three subnets manually
- Create a production VPC with three subnets manually
- Create a bastion that is connected to both VPCs
- Create a development Cloud SQL Instance and connect and prepare the WordPress environment
- Create a Kubernetes cluster in the development VPC for WordPress
- Prepare the Kubernetes cluster for the WordPress environment
- Create a WordPress deployment using the supplied configuration
- Enable monitoring of the cluster via stackdriver
- Provide access for an additional engineer

Some Jooli Inc. standards you should follow:

- Create all resources in the {REGION} region and {*ZONE*} zone, unless otherwise directed.
- Use the project VPCs.
- Naming is normally *team-resource*, e.g. an instance could be named **kraken-webserver1**.
- Allocate cost effective resource sizes. Projects are monitored and 
  excessive resource use will result in the containing project's 
  termination (and possibly yours), so beware. This is the guidance the 
  monitoring team is willing to share: unless directed, use `e2-medium`.

### The Challenge

ou need to help the team with some of their initial work on a new 
project. They plan to use WordPress and need you to set up a development
 environment. Some of the work was already done for you, but other parts
 require your expert skills.

As soon as you sit down at your desk and open your new laptop you 
receive the following request to complete these tasks. Good luck!

#### Environment

![Google Cloud environment, Team Griffin Infrastructure diagram](https://cdn.qwiklabs.com/UE5MydlafU0QvN7zdaOLo%2BVxvETvmuPJh%2B9kZxQnOzE%3D)

## Task 1. Create development VPC manually

- Create a VPC called `griffin-dev-vpc` with the following subnets only:
  
  - `griffin-dev-wp`
    - IP address block: `192.168.16.0/20`
  - `griffin-dev-mgmt`
    - IP address block: `192.168.32.0/20`

```
gcloud compute networks create griffin-dev-vpc --project=qwiklabs-gcp-00-f4c0a46d555c --description=griffin-dev-vpc --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional
```


```
gcloud compute networks subnets create griffin-dev-wp --project=qwiklabs-gcp-00-f4c0a46d555c --description=griffin-dev-wp --range=192.168.16.0/20 --stack-type=IPV4_ONLY --network=griffin-dev-vpc --region=us-east1
```


```
gcloud compute networks subnets create griffin-dev-mgmt --project=qwiklabs-gcp-00-f4c0a46d555c --range=192.168.32.0/20 --stack-type=IPV4_ONLY --network=griffin-dev-vpc --region=us-east1
```



gcloud compute networks create griffin-dev-vpc --project=qwiklabs-gcp-00-3d36027475b7 --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional && gcloud compute networks subnets create griffin-dev-wp-us-central1 --project=qwiklabs-gcp-00-3d36027475b7 --range=192.168.16.0/20 --stack-type=IPV4_ONLY --network=managementnet --region=us-central1 && 
gcloud compute networks subnets create griffin-dev-mgmt-us-central1 --project=qwiklabs-gcp-00-3d36027475b7 --range=192.168.32.0/20 --stack-type=IPV4_ONLY --network=managementnet --region=us-central1

## Task 2. Create production VPC manually

- Create a VPC called `griffin-prod-vpc` with the following subnets only:
  
  - `griffin-prod-wp`
    - IP address block: `192.168.48.0/20`
  - `griffin-prod-mgmt`
    - IP address block: `192.168.64.0/20`

From Google command line:

```
gcloud compute networks create griffin-prod-vpc --project=qwiklabs-gcp-00-f4c0a46d555c --description=griffin-prod-vpc --subnet-mode=custom --mtu=1460 --bgp-routing-mode=regional

```


```
gcloud compute networks subnets create griffin-prod-wp --project=qwiklabs-gcp-00-f4c0a46d555c --description=griffin-prod-wp --range=192.168.48.0/20 --stack-type=IPV4_ONLY --network=griffin-prod-vpc --region=us-east1

```

```
gcloud compute networks subnets create griffin-prod-mgmt --project=qwiklabs-gcp-00-f4c0a46d555c --description=griffin-prod-mgmt --range=192.168.64.0/20 --stack-type=IPV4_ONLY --network=griffin-prod-vpc --region=REGION

```



  



## Task 3. Create bastion host

- Create a bastion host with two network interfaces, one connected to `griffin-dev-mgmt` and the other connected to `griffin-prod-mgmt`. Make sure you can SSH to the host.

## Task 4. Create and configure Cloud SQL Instance

1. Create a **MySQL Cloud SQL Instance** called `griffin-dev-db` in .
2. Connect to the instance and run the following SQL commands to prepare the **WordPress** environment:

```
CREATE DATABASE wordpress;
CREATE USER "wp_user"@"%" IDENTIFIED BY "stormwind_rules";
GRANT ALL PRIVILEGES ON wordpress.* TO "wp_user"@"%";
FLUSH PRIVILEGES;
```

These SQL statements create the worpdress database and create a user with access to the wordpress database.

You will use the username and password in task 6.
