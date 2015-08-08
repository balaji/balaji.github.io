---
layout: post
title:  "AWS Elastic Beanstalk ,Docker and Nginx"
date:   2015-08-08 23:02:00
categories: 
---

AWS Elastic Beanstalk is the equivalent of 'rails'[1] in AWS world to deploy a project (almost in any stack) as quickly as possible without handling the low level details such as web servers, reverse proxies, load balancers, security groups, auto scaling groups, etc. So when you intend to host a cluster of nginx reverse proxies, AWS Elastic Beanstalk seems to a good choice, but unfortunately there aren't much literature out there on how to do this.

## Docker

So we need to treat nginx as an application deployed on a cluster of machines managed by a load balancer, may be within VPC and auto scaling rules. Docker is a good option in such a case because it makes no assumptions about the kind of application that will be deployed on top of it and AWS Elastic Beanstalk has first class support for dockers.

# Single container Docker vs Multi-container Docker

AWS Elastic Beanstalk supports both single container and multi container dockers. The difference being that: single container docker uses the first port in the `EXPOSE` directive in docker file and maps it to port 80 and it will ingore any other ports. Multi container docker has provision to expose multiple ports out of a docker image and also map them to multiple load balancers (needs scripting). In this blog post, I'll be talking only about *Single container* docker.


# Step 1 - Dockerfile & Dockerrun.aws.json

    FROM ubuntu:trusty
    RUN apt-get update
    RUN apt-get install -y nginx nginx-extras

    RUN rm -vf /etc/nginx/nginx.conf

    COPY nginx.conf /etc/nginx/
    RUN echo "daemon off;" >> /etc/nginx/nginx.conf

    EXPOSE 80

    CMD ["service", "nginx" "start"]

The above `Dockerfile` is self-explanatory. It downloads the ubuntu trusty image, installs nginx and nginx-extras.
Copies our customized `nginx.conf` file and starts the nginx server.

# Step 2 - Dockerrun.aws.json file

AWS Elastic Beanstalk requires a Dockerrun.aws.json file for deployment of docker containers. There are two formats. One for single container and other for multi container docker environments. 

    {
      "AWSEBDockerrunVersion": "1",
      "Volumes": []
    }

The above code is the most basic file that you need for docker deployment. More detailed options are in AWS docmentation.

# Step 3 - Done

Thats all you need to deploy a 'nginx application' to AWS Elastic Beanstalk with all this goodness.

# But... What about other things - like Environment specific configuration, etc.

