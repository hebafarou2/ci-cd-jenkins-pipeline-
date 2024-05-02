# Setting up CI/CD Pipeline with Jenkins on AWS EC2
This guide walks you through setting up a Continuous Integration/Continuous Deployment (CI/CD) pipeline using Jenkins on an AWS EC2 instance. The pipeline will integrate with a GitHub repository.
## Table of Contents 
1. [Create EC2 Instance on AWS](#create-ec2-instance-on-aws)
2. [prepare GitHub Repository](#prepare-github-repository)
3. [Install Git and Git Flow on EC2](#install-git-and-git-flow-on-ec2)
4. [Create Jenkinsfile and Push to GitHub](#create-jenkinsfile-and-push-to-github)
5. [Install Jenkins on EC2](#install-jenkins-on-ec2)
6. [Access Jenkins](#access-jenkins)
7. [Complete the Setup Wizard](#complete-the-setup-wizard)
8. [Integrate GitHub Repository to Jenkins Project](#integrate-github-repository-to-jenkins-project)
9. [Testing and Validation](#Testing-and-Validation)
## 1. Create EC2 Instance on AWS

  - Launch an EC2 instance on AWS                            
  - Edit security group settings                             
      - Add inbound rule to the security group for port 8080.  
        - type: custom TCP                                    
        - port :8080                                          
        - source: 0.0.0.0/0                                    
## 2. Prepare GitHub Repository:
  - Create or select a GitHub repository.
  - Initialize the repository with a README.md.
  - Ensure the repository contains at least two branches:
     develop for ongoing development master (or main) for stable releases.
  - Apply the Git Flow model.
## 3. Install Git and Git Flow on EC2
    # install git 
      $ sudo yum install git
      $ git --version
      $ git clone "repo_url"    
      $ cd "path/to/the/repo"
    # install git flow
      $ git clone --recursive https://github.com/petervanderdoes/gitflow-avh.git
      $ cd  gitflow-avh /contrib/
      $  chmod +x gitflow-installer.sh
      $   sudo ./gitflow-installer.sh install stable
      $ sudo yum update
      $ git flow init (keep defaults)
      $ git branch  (will have 2 branches main , develop)

## 4. Create Jenkinsfile and Push to GitHub
 ### Create a Jenkinsfile with pipeline stages (Build, Test, Deploy).
    
            
                1. nano Jenkinsfile
  
          pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }

   2.  save and exit 
### Push the Jenkinsfile to your GitHub repository.
      $ git add Jenkinsfile
      $ git commit -m "Add Jenkinsfile"
      $ git log   
      $ git push "repo_url" develop
### Note 
- while push it not respond becaues Authentication failed 
- so i create token from git hub   go to
  (settings /developer settings/ Personal access tokens /Tokens (classic)/Generate new token/Generate new token (classic)
-  Then  select all checkboxes ang generate it 
- git push "repo_url" develop      
- Take this token and paste it to password field
## 5. Install Jenkins on EC2
  ### install java 
      $ sudo yum install java-1.8.0-openjdk-devel -y
      $ java --version
  ### install jenkins 
    #Add the Jenkins Repository
      $ sudo wget -O /etc/yum.repos.d/jenkins.repo     https://pkg.jenkins.io/redhat-stable/jenkins.repo
    #Import a key file from Jenkins-CI to enable installation from the package
      $ sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
      $ sudo yum upgrade
    #Install Jenkins
      $ sudo yum install jenkins -y
      $ sudo systemctl enable jenkins
      $ sudo systemctl start jenkins

## 6. Access Jenkins
- open your web browser and access Jenkins by navigating to: 
    - http://your_ec2_instance_public_ip:8080
- You will see a setup wizard and be prompted to enter the administrator password.
- Retrieve the password with the following command:
    -  $ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
- Then copy password then paste to sign in 
## 7. Complete the Setup Wizard
1- Install Plugins       
2- Create the First Admin User     
3- Instance Configuration      
4- Finish the Setup          
5- Start Using Jenkins              

## 8. Integrate GitHub Repository to Jenkins Project
### Configure GitHub webhook to trigger Jenkins builds.
  - go to your GitHub repository and click on ‘Settings’, 
  - Click on Webhooks and then click on ‘Add webhook’,
  - In the ‘Payload URL’ field, paste your Jenkins environment URL (At the end of this URL add /github-webhook/)
  - In the ‘Content type’ select: ‘application/json’ 
  - Then select "just the push event"
### Configure Jenkins
 - Connect Jenkins to GitHub.
    - plugins : Go to "Manage Jenkins" > " Plugins" > "Available plugins" and install "GitHub Integration Plugin for jenkins".
     - Set up credentials in Jenkins for GitHub: "Manage Jenkins" > "Manage Credentials", Click on "Global credentials (unrestricted)" or another appropriate domain.
        (Click on "Add credentials" then enter username and token in github that in step 4)
  - Create a new pipeline job.
     - Select "New Item", name your pipeline (e.g., "GitHub Pipeline"), and choose "Pipeline" as the type.
     - In Build Triggers Check "GitHub hook trigger for GITScm polling".
     - In the pipeline configuration, select "Pipeline script from SCM" and choose "Git" as the SCM.
     - Enter the repository URL and credentials.
     - Specify the branch to build (e.g., */develop).
     - Save
## 9. Testing and Validation:
  - Push a change to the develop branch and verify Jenkins triggers a build.
  - Check the Jenkins dashboard for build status and output.
