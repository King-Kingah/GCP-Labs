### Task 1. Create a project jumphost instance
You will use this instance to perform maintenance for the project.

Requirements:

Name the instance nucleus-jumphost-795.
Create the instance in the us-west1-c zone.
Use an e2-micro machine type.
Use the default image type (Debian Linux).


```
gcloud compute instances create nucleus-jumphost-795 \
    --machine-type e2-micro \
    --zone us-west1-c
```



### Task 2. Set up an HTTP load balancer
You will serve the site via nginx web servers, but you want to ensure that the environment is fault-tolerant. Create an HTTP load balancer with a managed instance group of 2 nginx web servers. Use the following code to configure the web servers; the team will replace this with their own configuration later.

```
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```

You need to:

Create an `instance template`. Don't use the default machine type. Make sure you specify e2-medium as the machine type.

```
gcloud compute instance-templates create nucleus-web-server-template \
    --metadata-from-file startup-script=startup.sh \
    --network default\
    --machine-type e2-medium \
    --region us-west1
```

Create a 'Target Pool'

```
gcloud compute target-pools create nucleus-pool
    ---region us-west1
```

Create a `managed instance group` based on the template.

```
gcloud compute instance-groups managed create nucleus-webserver-group \
    --base-instance-name nucleus-webserver \
    --size 2 \
    --template nucleus-web-server-template \
    --region us-west1
```

Create a firewall rule named as `allow-tcp-rule-673` to allow traffic (80/tcp).

```
gcloud compute firewall-rules create allow-tcp-rule-673 \
    --allow tcp:80 \
    --network default
```

Create a health check.

```
gcloud compute http-health-checks create http-basic-check

```

Create a backend service and add your instance group as the backend to the backend service group with named port (http:80).

```
gcloud compute instance-groups managed \
    set-named-ports nucleus-webserver-group \
    --named-ports http:80 \
    --region us-west1
```

Create a URL map, and target the HTTP proxy to route the incoming requests to the default backend service.

```
gcloud compute backend-services create nucleus-webserver-backend \
    --protocol HTTP \
    --http-health-checks http-basic-check \
    --global
```
-- `Backend service` server that will use the `Health Check` to know when a server in not responding, and not Load balancing the request to it.
-- 1 backend in LoadBalancing table and connected to the health check

```
gcloud compute backend-services add-backend nucleus-webserver-backend \
    --instance-group nucleus-webserver-group \
    --instance-group-region us-west1 \
    --global
```
--Connect the backend to the instance group


```
gcloud compute url-maps create nucleus-webserver-map \
    --default-service nucleus-webserver-backend
```
--1 loadbalancing named nucleus-webserver-map

Create a target HTTP proxy to route requests to your URL map

```
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map nucleus-webserver-map
```
--1 proxy in target proxies


Create a forwarding rule.

```
gcloud compute forwarding-rules create http-content-rule \
    --global \
    --target-http-proxy http-lb-proxy \
    --ports 80
```
-- 1 frontend

```
gcloud compute forwarding-rules list
```




### RAW:


Updated [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-02-04c36a8ef9fe/zones/us-central1-c/instances/instance-1].
ip_address: 34.172.92.73
password:   L3\Opkh;C=;j9Q(
username:   admin

~~~~~~~~~~~~~~~~~~~~~~~~~
Task 1. Create a project jumphost instance:
Requirements:

    Name the instance: Instance name .
    Use an e2-micro machine type.
    Use the default image type (Debian Linux).
	
gcloud compute instances create nucleus-jumphost-146 --machine-type e2-micro --zone=$ZONE

gcloud compute instances list

gcloud compute instances list --filter="name=('gcelab2')"

Open the Cloud Console, go to Navigation menu > 

Task 2. Create a Kubernetes service cluster:
The team is building an application that will use a service running on Kubernetes. You need to:

    Create a zonal cluster using .
    Use the Docker container hello-app (gcr.io/google-samples/hello-app:2.0) as a placeholder; the team will replace the container with their own work later.
    Expose the app on port 

gcloud container clusters create --machine-type=e2-medium --zone=ZONE nucleus-cluster

gcloud container clusters get-credentials nucleus-cluster

kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0

kubectl expose deployment hello-server --type=LoadBalancer --port 8081

kubectl get service

Task 3. Set up an HTTP load balancer:
You need to:

    Create an instance template.
    Create a target pool.
    Create a managed instance group.
    Create a firewall rule named as to allow traffic (80/tcp).
    Create a health check.
    Create a backend service, and attach the managed instance group with named port (http:80).
    Create a URL map, and target the HTTP proxy to route requests to your URL map.
    Create a forwarding rule.

# Template
gcloud compute instance-templates create lb-backend-template \
   --region=Region \
   --network=default \
   --subnet=default \
   --machine-type=e2-medium \
   --image-family=debian-11 \
   --image-project=debian-cloud \
   --metadata=startup-script='#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - 
'"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html'


gcloud compute instances create nucleus-www1 \
    --zone=Zone \
    --tags=network-lb-tag \
    --machine-type=e2-small \
    --image-family=debian-11 \
    --image-project=debian-cloud \
    --metadata=startup-script='#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - 
'"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html'

gcloud compute instances create nucleus-www1 \
    --machine-type=e2-small \
    --image-family=debian-11 \
    --image-project=debian-cloud \
    --metadata=startup-script=./startup.sh


gcloud compute addresses create nucleus-lb-ip-1 \
  --region Region
  
gcloud compute http-health-checks create basic-check

gcloud compute target-pools create nucleus-pool \
  --region us-central1 --http-health-check basic-check


gcloud compute target-pools add-instances nucleus-pool \
    --instances nucleus-www1,nucleus-www2
	
	
gcloud compute forwarding-rules create www-rule \
    --region  Region \
    --ports 80 \
    --address network-lb-ip-1 \
    --target-pool www-pool
	
	
allow-tcp-rule-168


# create a target pool
gcloud compute target-pools create nginx-pool

# Create a managed instance group
gcloud compute instance-groups managed create nginx-group \
	--base-instance-name nginx \
	--size 2 \
	--template nginx-template \
	--target-pool nginx-pool

gcloud compute instances list

# create a firewall rule
gcloud compute firewall-rules create accept-tcp-rule-436 --allow tcp:80


# create a forwarding rule
gcloud compute forwarding-rules create nucleus-nginx-lb \
	--region us-central1 \
	--ports=80 \
	--target-pool nginx-pool
gcloud compute forwarding-rules list


# create a health check
gcloud compute http-health-checks create nucleus-http-basic-check

# create a backend service and attach the managed instance group
gcloud compute instance-groups managed \
	set-named-ports nginx-group \
	--named-ports http:80

gcloud compute backend-services create nginx-backend \
	--protocol=HTTP \
	--health-checks=nucleus-http-basic-check \
	--global

gcloud compute backend-services add-backend nginx-backend \
	--instance-group nginx-group \
	--instance-group-zone us-central1-a \
	--global

# create a url map and target the HTTP proxy
gcloud compute url-maps create nucleus-web-map \
	--default-service nginx-backend

gcloud compute target-http-proxies create http-lb-proxy \
	--url-map nucleus-web-map

# create a forwarding rule
gcloud compute forwarding-rules create http-content-rule \
	--global \
	--target-http-proxy http-lb-proxy \
	--ports 80
gcloud compute forwarding-rules list