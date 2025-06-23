              Containerizing the NodeJs application by implementing CI/CD tools.
________________________________________
STEP2: Create a Cluster
________________________________________
STEP 3: Install the Jenkins & Docker & SonarQube & Install the Trivy
________________________________________
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
PIPELINE VIEW:
 ![image](https://github.com/user-attachments/assets/7507242b-bdb7-48f4-a64b-b73a4a3941fe)

STEP 4: Install Prometheus & Grafana using helm
 Log in to ArgoCD UI
Access the ArgoCD web interface 
Create a New Application
  •	On the left sidebar, click "New App".
Fill in Application Details
  •	Application Name: A unique name for your app
  •	Project: Leave as default unless you've created a custom project.
  •	Sync Policy:
    o	Choose Manual 
    o	Automatic 
 Specify the Git Repository
  •	Repository URL: Paste your GitHub repo link 
  •	Revision: Select the branch 
  •	Path: Directory containing your Kubernetes manifests 
 Set the Deployment Destination
  •	Cluster URL: https://kubernetes.default.svc 
  •	Namespace: Choose where to deploy your app 
 Save and Deploy
  •	Click "Create"
  •	Your app will now appear in the dashboard
  •	If using Manual sync, click "Sync" to deploy
  •	If using Auto-sync, ArgoCD will deploy it automatically when it detects changes
ARGOCD dashboard

 ![image](https://github.com/user-attachments/assets/d9b07359-0eaf-44e8-9354-c02019cfc7fd)

