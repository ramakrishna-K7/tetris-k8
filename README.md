Containerizing the NodeJs application by implementing CI/CD tools🚀

In this project, we are deploying a Node.js application to a Kubernetes cluster by setting up KOPS. We will leverage various DevOps tools such as Jenkins, Docker, Trivy, SonarQube, and OWASP Dependency Check to ensure secure and high-quality software delivery. The entire process will be automated, enabling continuous integration and continuous deployment (CI/CD) within the Kubernetes environment, with monitoring and observability provided by Prometheus and Grafana. Additionally, we will use ArgoCD for efficient and declarative application deployment and management.
1.	KOPS: To create and manage the Kubernetes cluster on AWS.
2.	Jenkins: For continuous integration (CI) and automating build and deployment pipelines.
3.	Docker: To containerize the Node.js application for consistent and portable deployments.
4.	Trivy: A security scanner for container images to identify vulnerabilities.
5.	SonarQube: For static code analysis and ensuring code quality and security.
6.	OWASP Dependency Check: To detect vulnerable dependencies and improve software security.
7.	Kubernetes: The container orchestration platform for managing, scaling, and deploying the application.
8.	ArgoCD: A declarative GitOps tool for continuous deployment and application management on Kubernetes.
9.	Prometheus: For monitoring the health and performance of the Kubernetes cluster and application.
10.	Grafana: For visualizing metrics and monitoring data collected by Prometheus.
Together, these tools will enable a secure, automated, and efficient CI/CD pipeline for deploying and managing a Node.js application in a Kubernetes cluster.
Prerequisites
Before installing Kops, ensure you have the following:
●	AWS Account with IAM permissions to create EC2 instances, S3 buckets, and Route 53 DNS records.
●	kubectl installed to interact with the Kubernetes cluster.
●	AWS CLI configured with necessary credentials.
●	Kops installed on your EC2 system.
________________________________________
Steps to Create Cluster Using Kops
STEP 1: Launch EC2 Instance
1.	AMI: Amazon Linux Kernel 5.10
2.	Instance Type: t2.micro
3.	EBS: 20GB
4.	IAM Role: admin permissions
________________________________________
STEP 2: Set the Default Path

1.	vi ~/.bashrc 
2.	export PATH=$PATH:/usr/local/bin/
3.	Save and exit the file.
4.	Make this path active:
source ~/.bashrc
________________________________________
STEP 3: Install AWS CLI
1.	Download the AWS CLI:
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
2.	Unzip the downloaded file:
unzip awscliv2.zip
3.	Install AWS CLI:
sudo ./aws/install
________________________________________
STEP 4: Install kubectl
1.	Download the latest version of kubectl:
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
2.	Make it executable:
chmod +x kubectl
3.	Move it to the appropriate location:
sudo mv kubectl /usr/local/bin/
________________________________________
STEP 5: Install Kops
1.	Download the Kops binary:
wget https://github.com/kubernetes/kops/releases/download/v1.24.1/kops-linux-amd64
2.	Make it executable:
chmod +x kops-linux-amd64
3.	Move it to the appropriate location:
sudo mv kops-linux-amd64 /usr/local/bin/kops
________________________________________
STEP 6: Check the VersionsEnsure everything is installed correctly by checking the versions:
kops version
kubectl version

________________________________________
STEP 7: Create S3 Bucket & Enable Versioning
1.	Create an S3 bucket (ensure the bucket name is unique):
aws s3api create-bucket --bucket <bucket-name>
2.	Enable versioning on the S3 bucket:
aws s3api put-bucket-versioning --bucket <bucket-name> --versioning-configuration Status=Enabled
________________________________________
STEP 8: Export the Cluster State
Set the environment variable for the cluster state:
export KOPS_STATE_STORE=<bucket-name>

________________________________________
STEP 9: Create a Cluster
Use the following command to create your cluster:
kops create cluster --name <cluster-name> --zones us-east-1a --master-size t2.medium --master-count 1 --master-volume-size 20 --node-size t2.micro --node-count 2 --node-volume-size 20

________________________________________
STEP 10: Update the Cluster
After creating the cluster, update it with the following command:
kops update cluster --name <cluster-name> --yes --admin

________________________________________
STEP 11: Install the Jenkins

 ![image](https://github.com/user-attachments/assets/07119be9-8ad9-44ed-a0f2-58ad3afeaf4e)




STEP 12: Install the Docker

yum install docker -y
systemctl start docker
systemctl status docker
chmod 777 ///var/run/docker.sock
STEP 13: Install the SonarQube

docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

Now sonarQube is up & running on port: 9000

Access the sonarqube by <server-ip:9000>

Default credentials for sonarqube is 
username - admin
password - admin

After logging  with default credentials update in your password in sonar dashboard

 
![image](https://github.com/user-attachments/assets/ab71f6b3-dd05-487f-b814-f968e9708a89)


Now click on Manually and then create a Project

 




![image](https://github.com/user-attachments/assets/cd14e88d-2705-4041-9446-c2a805d7b3e1)








Now we need to integrate the sonarqube with jenkins for that click on the Jenkins

 ![image](https://github.com/user-attachments/assets/db718a09-4c5c-44b3-a606-c354d56e5f57)


After selecting the Jenkins then

1.	Select Github as our source code.
2.	For a step by step guide on installing and configuring those plugins in Jenkins, visit the Analysis Prerequisites documentation page.
 
![image](https://github.com/user-attachments/assets/adab1132-af22-4dd0-831d-03a90fad646d)


Configure Sonar with Jenkins
Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token
 
 ![image](https://github.com/user-attachments/assets/dcdec890-3893-4407-bafc-6d43cf62c22f)
 ![image](https://github.com/user-attachments/assets/431694b4-d43e-4860-a329-41193e8e2301)


Goto Jenkins Dashboard → Manage Jenkins → Credentials → System → Global credentials (unrestricted) → Add Secret Text
 
 ![image](https://github.com/user-attachments/assets/b85c293a-eb4e-4dd7-9373-d567cbfa6fe6)

STEP 14: Install the Trivy
Steps to Install Trivy:
1.	Download the Trivy release:
wget https://github.com/aquasecurity/trivy/releases/download/v0.18.3/trivy_0.18.3_Linux-64bit.t
2.	Extract the downloaded tar.gz file:
tar zxvf trivy_0.18.3_Linux-64bit.tar.gz
3.	Move the Trivy binary to /usr/local/bin/:
sudo mv trivy /usr/local/bin/
4.	Edit your .bashrc file to include /usr/local/bin/ in the PATH:
vim ~/.bashrc
5.	Add the following line to the .bashrc file:
export PATH=$PATH:/usr/local/bin/
6.	Reload the .bashrc to apply the changes:
source ~/.bashrc
With these steps, Trivy should be installed and ready to use!

Install the following dependencies on Jenkins
1.	Sonarqube Scanner
2.	Eclipse Temurin Installer
3.	NodeJS
4.	OWASP Dependency-Check
5.	Docker Pipeline
6.	Pipeline Stage View
7.	Sonar quality gate
Integrate all the tools to Jenkins
Go to Jenkins Dashboard → Manage Jenkins → Tools
JDK installations — v17
 
NodeJS installations — v 16
  node16.2.0
 

Dependency-Check installations
   dp-check6.5.1
 
STEP 14: Add Docker Hub Credentials

Goto Jenkins Dashboard → Manage Jenkins → Credentials → System → Global credentials (unrestricted) → Add Username and password
username : docker hub-username
password: docker hub-password
credentials id : docker-password

STEP 15: Create a Jenkins job
Goto Jenkins Dashboard create a job as My-Deployment Name, select pipeline and click on ok.

pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'mysonar'
    }
        stage ("git checkout") {
            steps {
                git branch: 'main', credentialsId: 'Github_user', url: 'https://github.com/ramakrishna-admin/tetris.git'
            }
        }
        stage("code quality") {
            steps{
                withSonarQubeEnv('mysonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=tetris \
                    -Dsonar.projectKey=tetris '''
                }
            }
         }
        stage ("Install dependencies") {
            steps {
                sh 'npm install'
            }
        }
        stage ("check dependencies") {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'Dp-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
         }
        stage ("Build image") {
            steps {
                sh 'docker build -t image1 .'
            }
        }
       stage ("trivy image scan") {
           steps {
                sh "trivy fs . > trivyfs.txt"
                sh 'trivy image image1'
            }
        }
        stage("push to the dockerhub"){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'docker-password') {
                        sh "docker tag image1 ramakrishna737/game:v1"
                        sh "docker push ramakrishna737/game:v1"
                    }
                }
            }
         }
    }
}


If you click on Build Now pipeline will start again

 

STEP 15: Install ARGO CD USING HELM
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 
chmod 700 get_helm.sh
./get_helm.sh
helm version

Now we need to install Argo CD using helm
1. Create Argo CD Namespace:
kubectl create namespace argocd

2. Install Argo CD:
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

3. Verify All Argo CD Resources:
kubectl get all -n argocd



 
EXPOSE ARGOCD SERVER:
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
yum install jq -y
export ARGOCD_SERVER='kubectl get svc argocd-server -n argocd -o json | jq --raw-output '.status.loadBalancer.ingress[0].hostname''
echo $ARGOCD_SERVER
kubectl get svc argocd-server -n argocd -o json | jq --raw-output .status.loadBalancer.ingress[0].hostname
The above command will provide load balancer URL to access ARGO CD
 


TO GET ARGO CD PASSWORD:
export ARGO_PWD='kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d'
echo $ARGO_PWD
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
The above command to provide password to access argo cd
Now we need to create our application in argo cd to deploy our application.


 

 

 

 
 
Deployed website and you can access through 
<server-ip:port>
 




