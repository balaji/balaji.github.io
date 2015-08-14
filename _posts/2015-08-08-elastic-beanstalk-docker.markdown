---
layout: post
title:  "AWS Elastic Beanstalk ,Docker and Nginx"
date:   2015-08-08 23:02:00
categories: Programming
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

**Update**

Most deployment scenarios will not be this simple and we would have to handle environment specific constraints such as server names.

# Step 4 - Environment specific configuration

Typically in a AWS Elastic Beanstalk environment environment specific configuration are handled in the `configurations` tab and they would be available to the application.

Say for example, if we have test and production specific services and want two different nginx environments to handle them.

A typical Dockerfile configuraiton would become:

    FROM ubuntu:trusty

    RUN apt-get update
    RUN apt-get install -y nginx nginx-extras

    RUN rm -vf /etc/nginx/nginx.conf
    RUN rm -vf /etc/nginx/conf.d/upstream-services-${API_ENVIRONMENT}.conf

    COPY nginx.conf /etc/nginx/
    COPY upstream-services-${API_ENVIRONMENT}.conf /etc/nginx/conf.d/
    RUN echo "daemon off;" >> /etc/nginx/nginx.conf

    EXPOSE 8081

    CMD ["/usr/bin/supervisord"]

where `API_ENVIRONMENT` is defined in Elastic Beanstalk. We will have `upsteram-services-test.conf` and `upstream-services-production.conf` in source to handle environment specific upstream servers while keeping the common rules in `nginx.conf`

e.g. upstream-services-test.conf

    upstream test {
      server invoice-test.example.com;
    }

upstream-services-production.conf

    upstream test {
      server invoice-prod.example.com;
    }

But this is being a pre-built docker image, those environment configurations will not be available to docker at *build* time. Most probably you need those configuration settings at *build* time. So, here's a nice script that can read AWS Elastic Beanstalk environment configuration and write it do Dockerfile before building the docker image.

So, what we need is a way to edit dockerfile to pass environment variables *before* it gets built into an container image.
The below code snippet is an `.ebextensions` config file for AWS Elastic Beanstalk that creates a script file and executes it during the docker build process. Most of the code for this script is from this stackoveflow answer: [http://stackoverflow.com/a/30359445](http://stackoverflow.com/a/30359445)

    files:
      "/opt/elasticbeanstalk/hooks/appdeploy/pre/02export_env_vars_on_host.sh":
        mode: "000755"
        owner: root
        group: root
        content: |
          #!/usr/bin/env bash
          echo Defaults:root \!requiretty >> /etc/sudoers
          for envvar in `jq '.optionsettings | {"aws:elasticbeanstalk:application:environment"}[] | .[]' /opt/elasticbeanstalk/deploy/configuration/containerconfiguration`
          do
            temp="${envvar//\"/}";
            temp="${temp/=/ }";
            sed -i "/FROM ubuntu:trusty/a ENV $temp" /var/app/current/Dockerfile
          done

The above code snippet, reads environment specific variables from AWS Elastic Beanstalk and inserts them as `ENV` directives to `Dockerfile` before it gets built. This is important and as of writing `appdeploy/pre/02` is a safe position as `01` step is unzip of source code and `03` step is the actual build process of docker container.

#Step 5: Running multiple processes

Docker images should be treated as 'processes' and a single container should ideally run only on process.
But almost in all cases, we would need more than one process to run, say for example - monitoring. In such cases, it would be better to use a process control system such as [supervisord](http://supervisord.org/)
