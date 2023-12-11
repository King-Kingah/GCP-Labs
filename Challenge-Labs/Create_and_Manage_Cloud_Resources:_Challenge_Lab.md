# Create and Manage Cloud Resources: Challenge Lab

## Overview
In a challenge lab you’re given a scenario and a set of tasks. Instead of following step-by-step instructions, you will use the skills learned from the labs in the quest to figure out how to complete the tasks on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

**Topics tested:**

 - Create an instance
 - Create a 3-node Kubernetes cluster and run a simple service
 - Create an HTTP(s) load balancer in front of two web servers

#### Task 1: Create a project jumphost instance
Run command:

gcloud compute instances create nucleus-jumphost \
          --network nucleus-vpc \
          --zone us-east1-b  \
          --machine-type f1-micro  \
          --image-family debian-9  \
          --image-project debian-cloud \
          --scopes cloud-platform \
          --no-address


#### Task 2: Create a Kubernetes service cluster
Run command:

gcloud container clusters create nucleus-backend \
          --num-nodes 1 \
          --network nucleus-vpc \
          --region us-east1
gcloud container clusters get-credentials nucleus-backend \
          --region us-east1

kubectl create deployment hello-server \
          --image=gcr.io/google-samples/hello-app:2.0

kubectl expose deployment hello-server \
          --type=LoadBalancer \
          --port 8080

#### Task 3: Set up an HTTP load balancer
Run command:

cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
