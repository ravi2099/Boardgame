# BoardgameListingWebApp

## Description

**Board Game Database Full-Stack Web Application.**
This web application displays lists of board games and their reviews. While anyone can view the board game lists and reviews, they are required to log in to add/ edit the board games and their reviews. The 'users' have the authority to add board games to the list and add reviews, and the 'managers' have the authority to edit/ delete the reviews on top of the authorities of users.  

## Technologies

- Java
- Spring Boot
- Amazon Web Services(AWS) EC2
- Thymeleaf
- Thymeleaf Fragments
- HTML5
- CSS
- JavaScript
- Spring MVC
- JDBC
- H2 Database Engine (In-memory)
- JUnit test framework
- Spring Security
- Twitter Bootstrap
- Maven

## Features

- Full-Stack Application
- UI components created with Thymeleaf and styled with Twitter Bootstrap
- Authentication and authorization using Spring Security
  - Authentication by allowing the users to authenticate with a username and password
  - Authorization by granting different permissions based on the roles (non-members, users, and managers)
- Different roles (non-members, users, and managers) with varying levels of permissions
  - Non-members only can see the boardgame lists and reviews
  - Users can add board games and write reviews
  - Managers can edit and delete the reviews
- Deployed the application on AWS EC2
- JUnit test framework for unit testing
- Spring MVC best practices to segregate views, controllers, and database packages
- JDBC for database connectivity and interaction
- CRUD (Create, Read, Update, Delete) operations for managing data in the database
- Schema.sql file to customize the schema and input initial data
- Thymeleaf Fragments to reduce redundancy of repeating HTML elements (head, footer, navigation)

## How to Run

1. Clone the repository
2. Open the project in your IDE of choice
3. Run the application
4. To use initial user data, use the following credentials.
  - username: bugs    |     password: bunny (user role)
  - username: daffy   |     password: duck  (manager role)
5. You can also sign-up as a new user and customize your role to play with the application! 😊

---
# Installation Process

## 1.Boardgame

- 1. Default vpc
- 2. Default SG (8 port open)
- 3. Create Instancess 6 (t2.medium, 25gb) + 1
	- Master Node
	- Slave-1
	- Slave-2
	- SonarQube
	- Nexus
  - Jenkins (t2 large,30)
  - Monitor

## Setup K8-Cluster using kubeadm [K8 Version-->1.28.1]


### Master & Worker Node
```
vim bp.sh
```
```bash
sudo apt-get update

sudo apt install docker.io -y
sudo chmod 666 /var/run/docker.sock

sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
sudo mkdir -p -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
```
chmod +x bp.sh
./bp.sh

sudo apt update

sudo apt install -y kubeadm=1.28.1-1.1 kubelet=1.28.1-1.1 kubectl=1.28.1-1.1
```
### Master Node
```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
//save a given kubeadm join token commadn and past into slave/worker node

**Slave/Worker Node**
//fire a command collect from masternode for connection

### Master Node
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml

kubectl get nodes
```
### Securty fot for tester
[Kube audit](https://github.com/Shopify/kubeaudit/releases)
```
wget https://github.com/Shopify/kubeaudit/releases/download/v0.22.2/kubeaudit_0.22.2_linux_amd64.tar.gz
tar -xvzf kubeaudit_0.22.2_linux_amd64.tar.gz
sudo mv kubeaudit /usr/local/bin/
kubeaudit all
```

---
## Other server Ready

### 1. Sonarqube Server
 1. install docker and access rootess mode

```
sudo apt update
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo apt-get install -y uidmap
dockerd-rootless-setuptool.sh install
```
 2. SonarQube Image run
 ```
 docker run -d --name Sonar -p 9000:9000 sonarqube:lts-community
 ```
 http://<server_ip>:9000/
 user:admin  pass:admin 

### 2. Nexus Server
 1. install docker and access rootess mode

```
sudo apt update
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo apt-get install -y uidmap
dockerd-rootless-setuptool.sh install
```
 2. Nexus Image Run
 ```
docker run -d --name Nexus -p 8081:8081 sonatype/nexus3
docker ps
docker exec -it <container id> sh

cd sonatype-work/nexus3
cat admin.password
```
**http://<server_ip>:8081/**
user : admin
pass : get from container

***Enable anonymous access***

### 3. Jenkisn Server

1. install docker and access rootess mode

```
sudo apt update
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo apt-get install -y uidmap
dockerd-rootless-setuptool.sh install
```
2. Install Trivy [1.05] (https://trivy.dev/v0.63/getting-started/installation/)
```
vim trivy.sh
```
```
sudo apt-get install wget gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```
```
sudo chmod +x trivy.sh
./trivy.sh
trivy --version
```
3. install jenkins
```
vim jenkin.sh
```
```bash

#!/bin/bash

# Install OpenJDK 17 JRE Headless
sudo apt install openjdk-17-jre-headless -y

# Download Jenkins GPG key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Add Jenkins repository to package manager sources
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update package manager repositories
sudo apt-get update

# Install Jenkins
sudo apt-get install jenkins -y

```
```
chmod +x jenkin.sh
./jenkin.sh
```
server_ip:8080
```
sudo cat /var/lin/jenkins/secrets/initialAdminPassword
```
 ### 4. Configuration and create Job into Jenkins

 - 1.Install plugins 

 	(Eclips Termurin Installer, Config File Provider, Maven Integration, Pipeline Maven Integration, sonarQube Scanner, Docker, Docker pipeline, Kubernets, kubernetes cli,kubernets Client API, Kubernets Credentials,Prometheus metrics)
 
 - 2. Configure Plugins (Dashboard > Manage Jenkins > tools) [Time : 0.55 mint-0.58]
  		
  	- JDK Install (name:jdk17, check install automatically, install from adoptium.net and add version jdk-17.0.9+9)
  	- SonarQube Scanner (name: sonar-scanner, version: )
  	- Maven (name: maven3, version: 3.6.1)
  	- Docker (name: docker, version: install automaticaly version latest)

  - apply

 - 3. Credential
  Manage Jenkins > Credentials > System > Global Credential
    
    - github [1.02 - 1.04]
    - sonarqube [1.08 - 1.10]
        - Quality Gate webhook , publisher [1.14 - 1.19]
    - nexus [1.18 - ]
      manage > configfiles > add config

    - k8s cluster secreate [1.32 - 1.35]
     k8-cred



 - 4. Create job [Time : 0.59 - ]
 		name: BoardGame , select Pipeline

 		Boardgame > Configuration
 		Configure Above Plugins in Jenkins
 [1.02 - 1.50]
 
```
pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    enviornment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/jaiswaladi246/Boardgame.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('SonarQube Analsyis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame \
                            -Dsonar.java.binaries=. '''
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                script {
                  waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token' 
                }
            }
        }
        
        stage('Build') {
            steps {
               sh "mvn package"
            }
        }
        
        stage('Publish To Nexus') {
            steps {
               withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                }
            }
        }
        
        stage('Build & Tag Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t adijaiswal/boardshack:latest ."
                    }
               }
            }
        }
        
        stage('Docker Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html adijaiswal/boardshack:latest "
            }
        }
        
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker push adijaiswal/boardshack:latest"
                    }
               }
            }
        }
        stage('Deploy To Kubernetes') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.8.146:6443') {
                        sh "kubectl apply -f deployment-service.yaml"
                }
            }
        }
        
        stage('Verify the Deployment') {
            steps {
               withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.8.146:6443') {
                        sh "kubectl get pods -n webapps"
                        sh "kubectl get svc -n webapps"
                }
            }
        }
        
        
    }
    post {
    always {
        script {
            def jobName = env.JOB_NAME
            def buildNumber = env.BUILD_NUMBER
            def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
            def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'

            def body = """
                <html>
                <body>
                <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                <h2>${jobName} - Build ${buildNumber}</h2>
                <div style="background-color: ${bannerColor}; padding: 10px;">
                <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                </div>
                <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                </div>
                </body>
                </html>
            """

            emailext (
                subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                body: body,
                to: 'jaiswaladi246@gmail.com',
                from: 'jenkins@example.com',
                replyTo: 'jenkins@example.com',
                mimeType: 'text/html',
                attachmentsPattern: 'trivy-image-report.html'
            )
        }
    }
}

}

```
### 5. k8s master node (https://github.com/jaiswaladi246/EKS-Complete/blob/main/Steps-eks.md)
 1.  create cluster service account
    [1.25-
    RBAC
    user-1 , role-1 (cluster admin access)
    user-2 , role-2 (good level of access)
    user-3 , role-3 (read only access)

```
kubectl crate ns webapps
```
```
vi svc.yaml
```
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```
```
kubectl apply -f svc.yaml
```
```
vi role.yaml
```
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - secrets
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```
```
kubectl apply -f role.yaml
```
```
vi bind.yaml
```

```yaml

apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins 

  ```
```
kubectl apply -f bind.yaml
```
```
vi sec.yaml 
```
```yaml
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins

```
```
kubectl apply -f sec.yaml -n webapps

kubectl describe secret mysecretname -n webapps

cd ~/.kube
ls
cat config
```
    server : ip

Modify deployment-service.yaml file in my project

  
### Kubelet install on jenkins server
```
vi kubelet.sh
```
```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```
```
chmod +x kubelet.sh
./kubelet.sh
```


### configure maile notification
google manage account > security > 2-step verification > app password
name : jenkins
generated pass: 

jenkins
manage jenkins > system
Extended E-mail Notification
 SMTP server: smtp.gmail.com
 SMTP POrt : 465

advance
 credential add jenkins
 username : abrahim.ctech@gmail.com
 password: genereted password
 id : mail-cred
 
 select abrahimctech@gmail.com(mail-cred)
 check use SSL

 E-mail Notification

 SMTP server
 advance use ssl
 SMTP port: 465

 use smtp authentication
 username : abrahim.ctech@gmail.com
 password: genereted password

 Test configuration
test email abrahim.ctech@gmail.com
 

### Monitoring part

```
sudo apt update -y
```
1. Install prometheuse (https://prometheus.io/download/)

```
wget https://github.com/prometheus/prometheus/releases/download/v3.5.0-rc.0/prometheus-3.5.0-rc.0.linux-amd64.tar.gz

ls
tar -xvf prometheus-3.5.0-rc.0.linux-amd64.tar.gz
rm -rf prometheus-3.5.0-rc.0.linux-amd64.tar.gz
cd prometheus prometheus-3.5.0-rc.0.linux-amd64
ls 
./prometheus &
```
public_ip:9090


2.Install Grafana(https://grafana.com/grafana/download)
```
sudo apt-get install -y adduser libfontconfig1 musl
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_12.0.2_amd64.deb
sudo dpkg -i grafana-enterprise_12.0.2_amd64.deb

sudo /bin/systemctl start grafana-server
```
public_ip:3000
user:admin pass:admin

3. Blackbos_exporter (https://prometheus.io/download/)
```
wget https://github.com/prometheus/blackbox_exporter/releases/download/v0.27.0/blackbox_exporter-0.27.0.linux-amd64.tar.gz

tar -xvf blackbox_exporter-0.27.0.linux-amd64.tar.gz
rm -rf blackbox_exporter-0.27.0.linux-amd64.tar.gz
cd blackbox_exporter-0.27.0.linux-amd64
ls ./backbox_exporter &
```
public_ip:9115


add into  prometheuse.yml file (https://github.com/prometheus/blackbox_exporter)
```
cd prometheus prometheus-3.5.0-rc.0.linux-amd64
ls 
vim prometheus.yml
```
```yml
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - http://prometheus.io    # Target to probe with http.
        - http://example.com:8080 # Target to probe with http on port 8080.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 127.0.0.1:9115  # The blackbox exporter's real hostname:port.

```
```
pgrep prometheus
kill id
./prometheus & 

```

4.Install Node exporter on jenkis server
```
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
ls 
tar -xvf node_exporter-1.9.1.linux-amd64.tar.gz
ls
rm rf node_exporter-1.9.1.linux-amd64.tar.gz
cd node_exporter-1.9.1.linux-amd64.tar.gz
ls
./node_exporter &

cd prometheus prometheus-3.5.0-rc.0.linux-amd64
ls 
vim prometheus.yml
```
```
- job_name: 'node_exporter'
    static_configs:
      - targets: ['IP:9100']

  - job_name: 'jenkisn'
    metrics_path: /prometheus
    static_configs:
      - targets: ['ip:8080']

```
```
pgrep prometheus
kill id
./prometheus &

```







