# **DevSecOps for Node.js Application Project :smile:**
### In this project we will create our Node.js application using Jenkins, security using SonarQube,OWASP and image scanning using Trivy. 
![project-diagram](https://github.com/sudhajobs0107/DevSecOps_ToDo_App/blob/main/images/DevSecOps-ToDo-App.gif)
___
## Tool we will need in this project is :-
>+ ### 1.) AWS EC2 or WSL 
>+ ### 2.) Docker, Docker-compose and DockerHub
>+ ### 3.) Github  
>+ ### 4.) Jenkins
>+ ### 5.) SonarQube
>+ ### 6.) OWASP DC
>+ ### 7.) Trivy
>+ ### 8.) Email
___
# Prerequisites
### Before starting the project you should have these things in your system :-
>+ ### Account on AWS or WSL install 
>+ ### Account on GitHub
>+ ### Code (we will use code from this repository) : [click here for code](https://github.com/MayurPanchale/DevSecOps_Nodejs-App)

To install and configure Windows Subsystem for Linux (WSL) on Windows, follow these steps:

1. Enable WSL Feature on Windows
Option A: Using PowerShell
Open PowerShell as Administrator.

Run the following command to enable WSL:

bash
Copy code
wsl --install
This command will enable the necessary features, install the latest version of WSL, and download Ubuntu as the default distribution.

Restart your computer if prompted.

Option B: Manually via Windows Features
Open Control Panel.
Go to Programs > Turn Windows features on or off.
Enable both:
Windows Subsystem for Linux
Virtual Machine Platform
Click OK and restart your PC.
2. Install a Linux Distribution
After enabling WSL, you can install a Linux distribution from the Microsoft Store:

Open the Microsoft Store and search for a Linux distribution (e.g., Ubuntu, Debian, Kali Linux).
Click Get to install your preferred distribution.
Alternatively, you can install a specific version using PowerShell:

bash
Copy code
wsl --install -d <distribution-name>
For example:

bash
Copy code
wsl --install -d Ubuntu-20.04
3. Set WSL 2 as Default (Optional but Recommended)
By default, WSL 2 offers better performance. To set WSL 2 as the default version:

Open PowerShell and run:
bash
Copy code
wsl --set-default-version 2
If you already have WSL 1 installed and want to upgrade to WSL 2, run:

bash
Copy code
wsl --set-version <distribution-name> 2
4. Configure WSL
Once installed, you can configure your WSL environment:

Open WSL: Use wsl or the specific distribution name (e.g., ubuntu) in PowerShell or the command prompt.

Update Packages: Once inside your Linux terminal, update your packages:

bash
Copy code
sudo apt update && sudo apt upgrade
Check Available Distributions:

bash
Copy code
wsl -l -v
Set a Default Distribution:

bash
Copy code
wsl --set-default <distribution-name>
___

# **Part 1** : **Initial Setup and Deployment**
## STEP 1: Launch Instance
+ ### Create AWS EC2 instance (t2.large)
![Instance](https://github.com/MayurPanchale/DevSecOps_Nodejs-App/blob/main/images/instance.png)

___
## or search for Ubuntu on windows start & connect to WSL/Ubuntu 
+ ### After successfully connecting to the instance, it will look like this  
![Connect-Instance](https://github.com/MayurPanchale/DevSecOps_Nodejs-App/blob/main/images/ubuntu-instance.png)
# **Part 2** : **Setup Jenkins**
+ ### Now to install Jenkins, first we need Java install because Jenkins need Java so for to install Java use command :-
```
sudo apt upgrade && sudo apt upgrade -y
sudo apt install fontconfig openjdk-17-jre
```
+ ### To check Java version use command :-
```
java --version
```
+ ### After installing Java, we will install Jenkins so for to install Jenkins use command :-
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
+ ### To check Jenkins status, use command :-
```
sudo service jenkins status
```
+ ### Now copy **Public IPv4 address:8080** and we will be on **Unlock Jenkins page**.
![unlocakjenkins](https://github.com/MayurPanchale/DevSecOps_Nodejs-App/blob/main/images/unlock-jenkins.png)
+ ### To unlock jenkins, use command :-
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
+ ### And we will get our password. Copy and paste it to unlock Jenkins → Now click Install suggested plugins → Fill details → Welcome to Jenkins
![jenkins-dashboard](https://github.com/MayurPanchale/DevSecOps_Nodejs-App/blob/main/images/jenkins-welcome.png)
# **Part 3** : **Setup Docker and Docker-Compose**
+ ### Now to install docker and docker-compose use command as follow :-
```
sudo apt-get update
sudo apt-get install docker.io docker-compose -y
sudo usermod -aG docker $USER
```
+ ### Also add jenkins to docker group for this use command as follow :-
```
sudo usermod -aG docker jenkins
sudo reboot
```
+ ### Check docker version use command  :-
```
docker version
```
+ ### Now enable docker for this use command  :-
```
sudo systemctl enable docker
```
# **Part 4** : **Setup SonarQube**
+ ### Now we will build SonarQube container for this use command :-
```
docker run -itd --name sonarqube -p 9000:9000 sonarqube:lts-community
```
+ ### Now if we will do **docker ps** so we will see our SonarQube container is running.
![sonar-container](https://github.com/MayurPanchale/DevSecOps_Nodejs-App/blob/main/images/sonar-container.png)
+ ### Now open port no. 9000 and copy Public IPv4 address and paste in new tab **Public IPv4 address:9000** and we will be on **Unlock SonarQube page**.
+ ### Now enter login and password=admin and it will give us screen to reset our password and we will be on **SonarQube page**.
![sonar-dashboard]()
+ ### Now to make devsecops pipeline, we have create user on sonarqube and this user will have access given to jenkins. To add user in sonar, Go to SonarQube → Administrator → Security → Users → Tokens → Update Tokens → name "jenkins" → Generate.
+ ### Our sonarqube setup done. Now to put sonar into jenkins we have to Install Plugins. To install sonarqube plugins **Go to Manage Jenkins → Plugins → Available Plugins** → Search **SonarQube Scanner** → install this plugin.
### Now SonarQube token put in Jenkins → Go to Manage Jenkins → click on Credentials → System → Global credentials → Add Credentials → Secret text → in Secret put the token that we copied from SonarQube → ID "Sonar" → Description "Sonar" → Create.
### Same as Sonar add Docker Credentials in Jenkins → Go to Manage Jenkins → click on Credentials → System → Global credentials → Add Credentials → Username with password → in Secret put your username and password of dockerhub → ID "DockerHub" → Description "DockerHub" → Create.
### Now we will link our SonarQube with Jenkins, for this Go to Manage Jenkins → System → Find SonarQube servers → Add SonarQube → Name "Sonar" → Server URL "http://172.25.41.249:9000" → Server authentication token "Sonar" → click on Apply and Save.
### We added our SonarQube Server to Jenkins.
### Now to enable Sonar Scanner Go to Manage Jenkins → Tools → Find SonarQube Scanner installations → Add SonarQube Scanner → name "Sonar" → Version "latest" → click on Apply and Save.
### Now we will create sonar webhooks. Go to Sonar → Administrator → Webhooks → Create → Name "jenkins" → URL "http://172.25.41.249:8080/sonarqube-webhook/" → Create.
