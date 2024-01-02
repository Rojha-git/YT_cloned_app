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
   
   check prometheus http://<ip_addr_monitoring_server>:9090  #if url is not came up check status of service if not running start it using

   $ sudo systemctl start prometheus

   check grafana http://<ip_addr_monitoring_server>:3000

   --Commands to check services
   $ sudo systemctl status prometheus
   
   $ sudo systemctl status node_exporter
   
   $ sudo systemctl status grafana-server

6. login to jenkins console:
   
   using http://<ip_addr_jenkins_server>:8080
   
   primary password you can copy from the server using "sudo cat /var/lib/jenkins/secrets/initialAdminPassword" - Enter the Administrator password
   
   after that you can create your userid and password.

8. after login to jenkins first of all click on "manage plugins" and install required plugins for Docker,Sonar,Nodejs and restart jenkins.
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

          
    
       
    
             
   

   





   
   


   
   
  
