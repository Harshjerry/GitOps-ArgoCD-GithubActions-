# Jenkins-ArgoCD pipeline

![Screenshot 2023-03-28 at 9 38 09 PM](https://user-images.githubusercontent.com/43399466/228301952-abc02ca2-9942-4a67-8293-f76647b6f9d8.png)


## Installation on EC2 Instance


Install Jenkins, configure Docker as agent, set up cicd, deploy applications to k8s and much more.

## AWS EC2 Instance

- Go to AWS Console
- Instances(running)
- Launch instances

<img width="994" alt="Screenshot 2023-02-01 at 12 37 45 PM" src="https://user-images.githubusercontent.com/43399466/215974891-196abfe9-ace0-407b-abd2-adcffe218e3f.png">

### Install Jenkins.

Pre-Requisites:
 - Java (JDK)

### Run the below commands to install Java and Jenkins

Install Java

```
sudo apt update
sudo apt install openjdk-17-jre
```

Verify Java is Installed

```
java -version
```

Now, you can proceed with installing Jenkins

```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules as show below.

- EC2 > Instances > Click on <Instance-ID>
- In the bottom tabs -> Click on Security
- Security groups
- Add inbound traffic rules as shown in the image (you can just allow TCP 8080 as well, in my case, I allowed `All traffic`).

<img width="1187" alt="Screenshot 2023-02-01 at 12 42 01 PM" src="https://user-images.githubusercontent.com/43399466/215975712-2fc569cb-9d76-49b4-9345-d8b62187aa22.png">


### Login to Jenkins using the below URL:

http://<ec2-instance-public-ip-address>:8080    [You can get the ec2-instance-public-ip-address from your AWS EC2 console page]

Note: If you are not interested in allowing `All Traffic` to your EC2 instance
      1. Delete the inbound traffic rule for your instance
      2. Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Click on Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the Jenkins to Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2023-02-01 at 11 02 09 AM" src="https://user-images.githubusercontent.com/43399466/215959757-403246c8-e739-4103-9265-6bdab418013e.png">

Jenkins Installation is Successful. You can now starting using the Jenkins 

<img width="990" alt="Screenshot 2023-02-01 at 11 14 13 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Install the Docker Pipeline plugin in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

Wait for the Jenkins to be restarted.


## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.
![image alt](https://github.com/Harshjerry/GitOps-ArgoCD-Jenkins/blob/639dc4fcf6e62ee9357d1c67f5ac116171119e80/Screenshot%202025-07-23%20125515.png)
![image alt](https://github.com/Harshjerry/GitOps-ArgoCD-Jenkins/blob/639dc4fcf6e62ee9357d1c67f5ac116171119e80/Screenshot%202025-07-23%20104710.png)
![image alt](https://github.com/Harshjerry/GitOps-ArgoCD-Jenkins/blob/2ce163d9f3d0e8c1a5951957f2c4a5523a827ed7/Screenshot%202025-07-23%20104431.png)


# GitOps-ArgoCD-GithubActions-
Gitops uses git as single  source of truth  to deliver applications and   infrastructure

GitOps also tck changes too your cluster i.e version  control too


Actor---> Kubernetes manifests(**GiT**) -----> **ArgoCD** ----->**K8s**
     updates                           reads         deploy


Gitops principle 
1. Declarative
2. Verisoned and immutable
3.  Pulled automatically
4. Continuosly Reconciled


 further ArgoCD does not let to change cluster mannually you have to  push chnges in git repo to do that 
  it would deny tha changes or   trace them back to previous state 


     GIT----- connect to git andd get state------->**Repo Server**
               Application controller-----connect to K8S and get state---->Kubernetes


we can communicate with argocd using CLI  by API server

A. configure instance ,install  java runtime in it  because jenkins is java application
 
 sudo apt update
sudo apt install openjdk-17-jre

B. Proceed to   install and start jenkins server

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

C. jenkins server by default listen at port **8080**

ADMIN password is by default in this dir
 cat /var/lib/jenkins/secrets/initialAdminPassword

Reference  d1d71c44e7cd455981def794385e1caa


//Using Docker agent for  jenkns  pipeline is efficient
1.  it save  money as  we won't run irautal memor 
2. contianers are delteed after exeutig pipeline  therefore efficient
3. No unneccesary provision of resources or instances




 **Configure a Sonar Server locally**
System Requirements
Java 17+ (Oracle JDK, OpenJDK, or AdoptOpenJDK)
Hardware Recommendations:
   Minimum 2 GB RAM
   2 CPU cores
sudo apt update && sudo apt install unzip -y
adduser sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
unzip *
chown -R sonarqube:sonarqube /opt/sonarqube
chmod -R 775 /opt/sonarqube
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
Hurray !! Now you can access the SonarQube Server on http://<ip-address>:9000


  in sonarqube  create token for jenkins to authenticat to sonarqube
  squ_7717f10640c6217f09bc19a3af4e57919307ead4

   you can restart  jenkins  using   {jenkinsurl:8080}/restart


//TO switch user  
sudo su - username


run argocd  pods 
 than   acccess service by port forwarding and using instnce ip

 further  password can be seen by       exmaple-ccluster-argocd  secret 
 3cE4bSBUhGXZ0pTAKPIxust9goWf5lMe







 //Automating   github intergation with jenkins

 Part 1: Configure GitHub to Send the "Alarm" (Webhook)
 
Go to Your GitHub Repository.
Click on the Settings tab.
In the left menu, click on Webhooks.
Click the Add webhook button.

Now, you need to fill out the form:
Payload URL: 
http://<YOUR_JENKINS_PUBLIC_URL>/github-webhook/

Content type: Select application/json.\

Which events would you like to trigger this webhook? Keep the default, Just the push event. This is exactly what you want: the pipeline should run whenever someone pushes new code.

Click Add webhook.

After you add it, GitHub will send a test "ping" event to your Jenkins URL. If everything is configured correctly, you will see a green checkmark next to it in the webhook delivery log.

Part 2: Configure Jenkins to Listen for the "Alarm"

Install the GitHub Plugin: First, make sure you have the necessary plugin.
Go to Manage Jenkins -> Plugins -> Available plugins.
Search for and install the GitHub Integration plugin. (You likely already have it, but it's good to check).
Configure Your Jenkins Pipeline Job:
Go to your pipeline job's main page.
Click on Configure in the left menu.
Find the "Build Triggers" Section:
Scroll down to the Build Triggers section in the configuration page.
Check the box for GitHub hook trigger for GITScm polling.
This is the option that tells Jenkins: "Activate the special listening endpoint (/github-webhook/) for this job. Don't poll continuously; just wait for the signal from GitHub."
Save your configuration.
That's it! Your Jenkinsfile does not need to be changed. All your existing logic for building, testing, and updating manifests will work perfectly. The only thing that has changed is what starts the process.