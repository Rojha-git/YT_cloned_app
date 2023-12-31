## Build and Deploy a Modern YouTube Clone Application in React JS with Material UI 5
1.create an ec2(t2.micro) instance on AWS and after ssh to this instance install terraform on that using below commands:
    #Confirm the latest version number on the terraform website:
    https://www.terraform.io/downloads.html
    
    $ wget https://releases.hashicorp.com/terraform/1.0.7/terraform_1.0.7_linux_amd64.zip
    
    $ unzip terraform_1.0.7_linux_amd64.zip
    
    $ sudo mv terraform /usr/local/bin/
    
    $ terraform --version


2. clone the git repo for terraform scripts:

    $ git clone https://github.com/Rojha-git/Terraform_YT_cloned_app.git
   
    $ cd Terraform_YT_cloned_app #under this we have two repo so choose one by one and run below command.
   
    $ terraform init          # aws should be configure with your instances
   
    $ terraform plan
   
    $ terraform apply
    
4. check the status of the installed services:

   check jenkins http://<ip_addr_jenkins_server>:8080

   check sonar http://<ip_addr_jenkins_server>:9000
   
   check prometheus http://<ip_addr_monitoring_server>:9090  #if url is not came up check status of service if not running start it using $ sudo systemctl start prometheus

   check grafana http://<ip_addr_monitoring_server>:3000

6. login to jenkins console:
   
   using http://<ip_addr_jenkins_server>:8080
   
   primary password you can copy from the server using "sudo cat /var/lib/jenkins/secrets/initialAdminPassword" - Enter the Administrator password
   
   after that you can create your userid and password.

8. after login to jenkins first of all click on "manage plugins" and install required plugins for Docker,Sonar,Nodejs and restart jenkins.
   also under the "tools" section configure jdk , nodejs , sonar scanner and under "system"  configure sonar server using server's console urls.

9. Generate the access token from sonarqube , github , dockerhub and configure all this under the jenkins "credentials" so that jenkins can access all these.

10. Under jenkins create an pipeline with "yt_cloned_ci-cd" name using below steps:

   I.   select SCM option and provide repo https://github.com/Rojha-git/YT_cloned_app.git to access jenkins file for ci-cd.
   II.

11. login to sonarqube console and configure quality gate , webhook , and create new project using jenkins server url.
12. 
       
   

   





   
   


   
   
  
