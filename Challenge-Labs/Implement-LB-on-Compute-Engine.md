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