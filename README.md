## Build and Deploy a Modern YouTube Clone Application in React JS with Material UI 5

 ![Complete CI-CD](https://github.com/Rojha-git/YT_cloned_app/blob/main/images/Jenkins-CI-CD.PNG)


## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Step-by-Step Guide](#step-by-step-guide)
  - [1. Create an EC2 Instance on AWS](#1-create-an-ec2-instance-on-aws)
  - [2. Install Terraform](#2-install-terraform)
  - [3. Clone the Terraform Scripts](#3-clone-the-terraform-scripts)
  - [4. Check the Status of Installed Services](#4-check-the-status-of-installed-services)
  - [5. Login to Jenkins Console](#5-login-to-jenkins-console)
  - [6. Configure Jenkins and Install Plugins](#6-configure-jenkins-and-install-plugins)
  - [7. Generate Access Tokens](#7-generate-access-tokens)
  - [8. Create Jenkins Pipeline](#8-create-jenkins-pipeline)
  - [9. SonarQube Configuration](#9-sonarqube-configuration)
  - [10. Setup Monitoring with Prometheus and Grafana](#10-setup-monitoring-with-prometheus-and-grafana)
  - [11. Jenkins Pipeline Execution](#11-jenkins-pipeline-execution)
  - [12. Create AWS EKS Cluster](#12-create-aws-eks-cluster)
  - [13. Integrate Prometheus with EKS and Import Grafana Dashboard](#13-integrate-prometheus-with-eks-and-import-grafana-dashboard)
  - [14. Automated Deployment with Jenkins on Kubernetes](#14-automated-deployment-with-jenkins-on-kubernetes)
  - [15. Webhook Configuration](#15-webhook-configuration)
- [Conclusion](#conclusion)
- [Acknowledgements](#acknowledgements)
- [License](#license)

## Introduction
This repository provides a step-by-step guide for building and deploying a modern YouTube clone application using React JS with Material UI 5. The guide covers setting up infrastructure on AWS, configuring Jenkins for CI/CD, integrating SonarQube for code analysis, and implementing monitoring with Prometheus and Grafana.

## Prerequisites
Before you begin, make sure you have the following prerequisites:
- AWS account
- GitHub account
- DockerHub account
- SonarQube account
- Gmail account for email notifications

## Step-by-Step Guide

1.create an ec2(t2.micro) instance on AWS and after ssh to this instance install terraform on that using below commands:
    #Confirm the latest version number on the terraform website:
    https://www.terraform.io/downloads.html
    
    $ wget https://releases.hashicorp.com/terraform/1.0.7/terraform_1.0.7_linux_amd64.zip
    
    $ unzip terraform_1.0.7_linux_amd64.zip
    
    $ sudo mv terraform /usr/local/bin/
    
    $ terraform --version


2.clone the git repo for terraform scripts-


    $ git clone "https://github.com/Rojha-git/Terraform_YT_cloned_app.git"
   
    $ cd Terraform_YT_cloned_app        #under this we have two repo so choose one by one and run below command.
   
    $ terraform init                    # aws should be configure with your instances
   
    $ terraform plan
   
    $ terraform apply
   
    
4.check the status of the installed services:


   check jenkins http://<ip_addr_jenkins_server>:8080

   check sonar http://<ip_addr_jenkins_server>:9000
   
   check prometheus http://<ip_addr_monitoring_server>:9090  #if url is not came up check status of service if not running start it using

   $ sudo systemctl start prometheus

   check grafana http://<ip_addr_monitoring_server>:3000

   --Commands to check services

   ```bash
   
   $ sudo systemctl status prometheus
   
   $ sudo systemctl status node_exporter
   
   $ sudo systemctl status grafana-server

   ```

6.login to jenkins console:
   
   using http://<ip_addr_jenkins_server>:8080
   
   primary password you can copy from the server using "sudo cat /var/lib/jenkins/secrets/initialAdminPassword" - Enter the Administrator password
   
   after that you can create your userid and password.

8. after login to jenkins first of all click on "manage plugins" and install required plugins for Docker, Sonar, Nodejs , kubernetes and restart jenkins.
   also under the "tools" section configure jdk , nodejs , sonar scanner and under "system"  configure sonar server using server's console urls.

9. Generate the access token from sonarqube , github , dockerhub and configure all this under the jenkins "credentials" so that jenkins can access all these.

10. Under jenkins create an pipeline with "yt_cloned_ci-cd" name using below steps:

     --- select SCM option and provide repo https://github.com/Rojha-git/YT_cloned_app.git to access jenkins file for ci-cd.

12. login to sonarqube console and configure quality gate , webhook , and create new project using jenkins server url, once all these completed then after the jenkins pipeline 
    completion you will be able to see all the flow and unit tests.

13. Login to prometheus using http://<ip_addr_monitoring_server>:9090   #username and password will be "admin"
    after login :
    
    --Add job for node exporter in prometheus
    
    $ cd /etc/prometheus/, ls, $ sudo nano prometheus.yml and below the job of prometheus, add job for node exporter

    
         - job_name: 'node_exporter'
           static_configs:
             - targets: ['IP-Address-monitoring:9100']

    
         - job_name: 'jenkins'
           metrics_path: '/prometheus'
           static_configs:
             - targets: ['IP-Address-jenkins:8080']
    

    --Check the indentatio of the prometheus config file with below command
    
    $ promtool check config /etc/prometheus/prometheus.yml

    --Reload the Prometheus configuration
    
    $ curl -X POST http://localhost:9090/-/reload 
    
15. After performing the #12 point you will be able to see the targets for matrices under the traget option in prometheus console.

16. login to grafana using http://<ip_addr_monitoring_server>:3000 --> #username and password will be "admin"

    --configure prometheus as the data source under the grafana using the prometheus url:

    --Import or add the dashboard for prometheus and jenkins by dashboard id of grafana   # you can try by google as well for dashboard id.


    **** Now you are able to access the monitoring console on grafana for both jenkins job and node_exporter server  ****
    
17. login to Jenkins console and run the jenkins job by commenting deployment stage /* stage('Deploy to Kubernets') */ 

    becuase we have have not created aws EKS cluster yet , by this we can test our pipeline till "docker build and push".

19. run the job , check if any issue came then try to troubleshoot.

20. configuration steps for generating gmail report --->>

    --login to your gmail account and search for app password under : " Account >> security " and genaerte the token

    --Go to jenkins console and configure this in jenkins credential using username and password(token).

    --under "Dashboard > Manage jenkins > system" configure email notification using smtp server "smtp.gmail.com" , port "465" and your gmail.

    --similliarly configure extended email notification using global credential that we have configured in jenkins using the same port and server as above.

      **** Once you trigger the job after this above configuration #18 , it will generate an mail ****



15. Create AWS EKS Cluster
    
    I.--Install kubectl on Jenkins Server
 
        $ sudo apt update
    
        $ sudo apt install curl
    
        $ curl -LO https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
 
        $ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

        $ kubectl version --client

    II. --Install AWS Cli
    
        $ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

        $ sudo apt install unzip

        $ unzip awscliv2.zip

        $ sudo ./aws/install

        $ aws --version

    III. --Installing  eksctl

        $ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
       
        $ cd /tmp
       
        $ sudo mv /tmp/eksctl /bin

        $ eksctl version

    IV.--Setup Kubernetes using eksctl
        $ eksctl create cluster --name virtualtechbox-cluster \
    
          --region ap-south-1 \
    
          --node-type t2.small \
    
          --nodes 3 \

    V.--Verify Cluster with below command

         $ kubectl get nodes    

          
16. --Integrate Prometheus with EKS and Import Grafana Monitoring Dashboard for Kubernetes
    
    I.-- Install Helm
    
       $ sudo snap install helm --classic
    
       $ helm version

    II. -- Install Prometheus on EKS cluster
    
       $ helm repo add stable https://charts.helm.sh/stable          ///We need to add the Helm Stable Charts for our local client

       $ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts     ///Add Prometheus Helm repo

       $ kubectl create namespace prometheus

       $ helm install stable prometheus-community/kube-prometheus-stack -n prometheus

       $ kubectl get pods -n prometheus

       $ kubectl get svc -n prometheus


    III. For exposing Prometheus to the external world using LoadBalancer
    
       $ kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus    #type:LoadBalancer, change port & targetport to 9090, 
 save and close

       $ kubectl get svc -n prometheus    //copy dns name of LB and browse with 9090    
       
17. After all the steps of #16 Login to grafana console: 

              -- Add data source in grafana using loadbalancer dns got from "$ kubectl get svc -n prometheus".
    
              -- Create or add dashboard for kubernets cluster in grafana #id you can check on google.

    
18. Till now we have testing all things for CI and monitoring for whole infrastructure.

    Now we are proceeding with CD Part for that we need to uncomment "stage('Deploy to Kubernets')"
    
    --So for that we can configure "argocd" on kuberberts cluster for automated deployment
                              **"OR"**
    --we can use jenkins for CD as well on kubernets.
    
    -- let's go by jenkins only , So as we already installed the plugins for k8s on jenkins so now we need to ssh jenkins server and copy the copy config file from /home/.kube/config and put it on your local system .

    -- Go to jenkins console and configure global credentials for 'kubernetes' using that 'config' file so that jenkins can authenticate or communicate with k8s cluster.

    -- Once all this done you can trigger your pipeline job and it will deploy your application on k8s.
 
20. Now for auto trigger we can configure "webhook" so for that :

    -- go to jenkins and select configure option for your pipeline , after that select "github project" and "github hook trigger" option.

    -- go to github and select "setting" for your application repo and configure webhook with jenkins url "http://"ip_addr":8080/github-webhook/

    -- configure git with your cli and verify auto trigger using below steps:

        $ git config --global user.name "your.name"

        $ git config --global user.email "your-email-address"

        $ git clone https://github.com/Rojha-git/YT-cloned-app.git

        $ cd a-youtube-clone-app

        $ git add .

        $ git commit -m "test change"

        $ git push origin main

    -- run "kubectl get svc" on k8s cluster you can find the dns for loadbalancer , by this url you can access your actual application.


    **Thankyou**
    **Happy learning**



   
   


   
   
  
