### CICD-with-Kubernetes

### Labs Server List
| Server Name        | Server Hostname               | Specs                           | IP Address     | Port Forwarding(ssh) | Port Forwarding(http) |
| ------------------ | ----------------------------- | ------------------------------- | -------------- | -------------------- | --------------------- |
| k8s-control        | k8s-control.ideacube.co.kr    | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.101 |  25 -> 22            |  -                    |
| worker-node-01     | worker-node-01.ideacube.co.kr | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.102 |  26 -> 22            |  -                    |
| worker-node-02     | worker-node-02.ideacube.co.kr | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.102 |  27 -> 22            |  -                    |

### Visual Studio Code & VirtualBox

          VirtualBox
          Visual Studio Code
          
          VBoxManage setextradata global GUI/Input/HostKeyCombination 162,164
          VBoxManage natnetwork add --netname NatNetwork --network "192.168.15.0/24" --enable --dhcp off --port-forward-4 "ssh:tcp:[]:25:[192.168.15.101]:22"
          VBoxManage natnetwork modify --netname NatNetwork --port-forward-4 "ssh1:tcp:[]:26:[192.168.15.102]:22"
          VBoxManage natnetwork modify --netname NatNetwork --port-forward-4 "ssh2:tcp:[]:27:[192.168.15.103]:22"

          VBoxManage natnetwork modify --netname NatNetwork --port-forward-4 "ssh3:tcp:[]:8080:[192.168.15.30]:8080"
          VBoxManage natnetwork modify --netname NatNetwork --port-forward-4 "ssh4:tcp:[]:80:[192.168.15.40]:80"
          VBoxManage natnetwork modify --netname NatNetwork --port-forward-4 "ssh5:tcp:[]:8081:[192.168.15.50]:80"
          
          Vboxmanage natnetwork list
              
          code --install-extension MS-CEINTL.vscode-language-pack-ko
          code --install-extension ms-vscode-remote.remote-ssh
          code --install-extension ms-azuretools.vscode-docker
          code --install-extension vscjava.vscode-java-pack
          code --install-extension vscjava.vscode-gradle
          code --install-extension vmware.vscode-boot-dev-pack

### Ubuntu 64bit Server 22.04.x(Minimized)
- After installing ubuntu 64 server minimum specifications
- Create User => user1/1234
          
          sudo su
          apt-get install net-tools iputils-ping
          printf "Server Name(Each Server)" > /etc/hostname
          printf "\n192.168.15.30 gitlab.ideacube.co.kr\n192.168.15.40 jenkins.ideacube.co.kr\n192.168.15.50 harbor.ideacube.co.kr\n192.168.15.101 k8s-control.ideacube.co.kr\n192.168.15.102 worker-node-01.ideacube.co.kr\n192.168.15.103 worker-node-02.ideacube.co.kr\n\n" >> /etc/hosts
          
          cat /etc/hosts
          cat /etc/hostname
          cat /etc/netplan/00-installer-config.yaml
          => check ip or change ip
          netplan apply

### Control

          kubeadm init --pod-network-cidr 10.10.0.0/16 --node-name k8s-control
          kubeadm token create --print-join-command
          
          curl -LO https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz     
          tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
          rm cilium-linux-amd64.tar.gz
          cilium install --set ipam.operator.clusterPoolIPv4PodCIDRList=10.10.0.0/16
          cilium status

          #################################
          # kubeadm 초기화
          $ sudo kubeadm reset
          $ sudo systemctl restart kubelet
          $ sudo reboot
          ##################################

### Etc
- Docker install Ubuntu
 
          sudo apt update && sudo apt full-upgrade
          sudo apt install apt-transport-https ca-certificates curl 
          
          # Add Docker's official GPG key:
          sudo apt-get update
          sudo apt-get install ca-certificates curl
          sudo install -m 0755 -d /etc/apt/keyrings
          sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
          sudo chmod a+r /etc/apt/keyrings/docker.asc
          
          # Add the repository to Apt sources:
          echo \
            "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
            $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
            sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
          sudo apt-get update
          
          sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
          
          sudo docker run hello-world
          sudo groupadd docker
          sudo usermod -aG docker $USER
          => logout
          newgrp docker
          docker run hello-world

- Java(17)

          sudo apt update
          sudo apt install fontconfig openjdk-17-jre
          java -version

- Git
 
          sudo apt install git
     
- Maven 

          sudo apt install maven

- Git config(Local)

          git config user.name "Dennis"
          git config user.email "itgenius1004@gmail.com"


- Git config(Global)

          git config --global user.name "Dennis"
          git config --global user.email "itgenius1004@gmail.com"

- Git Management

          git config --list
          git config --unset user.name
          git config --unset user.email

          git config --unset --global user.name
          git config --unset --global user.email

          git remote -v
          git push --force myapp-test
          
          git config credential.helper store
          git config credential.helper store --global
          
          git config --unset credential.helper
          git config --global --unset credential.helper


- Generate SSH Key
 
          ssh-keygen -t rsa -b 4096
          cd ~/.ssh
          cat id_rsa.pub >> ~/.ssh/authorized_keys
          cat authorized_keys
          cat id_rsa
          # Usage Visual Studio Code
          copy id_rsa on Host Windows(C:\Users\사용자\.ssh)

- Docker Security Issues

          // Security Issues 
          sudo chmod 666 /var/run/docker.sock or sudo chown root:docker /var/run/docker.sock
          sudo usermod -a -G docker jenkins

- Etc

          kubectl create -f nginx-pod.xml
          kubectl get pods
          kubectl get describe pod nginx
          kubectl port-forward nginx 9000:80
          kubectl delete pod nginx
          
          kubectl apply -f declarative-deployment.yaml
          kubectl get deployments
          kubectl apply -f declarative-deployment.yaml
          kubectl diff -f declarative-deployment.yaml
          
          kubectl get deployment nginx-declarative -o=yaml

  - ArgoCD

          kubectl create namespace argocd
          kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

          kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo


          kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
          kubectl delete namespace argocd
          
- Jenkins Script

          node {
              APP_NAME = 'myapp'
              RELEASE = '1.0.0'
              DOCKER_USER = 'dennis1945'
              DOCKER_PASS = 'dockerlogin'
              IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
              IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
              stage("Cleanup Workspace"){
                  cleanWs()
              }
              stage("Checkout from SCM"){
                  git branch: 'main', credentialsId: 'gitlab_idpwd', url: 'http://gitlab.ideacube.co.kr/root/myapp.git'
              }
              stage("Build Application"){
                  sh "mvn clean package"
              }
              stage("Test Application"){
                  sh "mvn test"
              }
              stage("Build & Push Docker Image") {
                  docker.withRegistry('https://index.docker.io/v1/',DOCKER_PASS) {
                      docker_image = docker.build "${IMAGE_NAME}"
                  }
                  docker.withRegistry('https://index.docker.io/v1/',DOCKER_PASS) {
                      docker_image.push("${IMAGE_TAG}")
                      docker_image.push('latest')
                  }
              }
              stage("SSH Into k8s Server") {
                  def remote = [:]
                  remote.name = 'k8s'
                  remote.host = 'k8s-control.ideacube.co.kr'
                  remote.user = 'user1'
                  remote.password = '1234'
                  remote.allowAnyHosts = true
                  
                  stage('Put myapp-deployment.yml') {
                      sshPut remote: remote, from: 'myapp-deployment.yml', into: '.'
                  }
                  stage('Deploy myapp') {
                      sshCommand remote: remote, command: "kubectl apply -f myapp-deployment.yml"
                  }
              }
       }

       -- Dokerfile
       FROM openjdk:17-slim
       ARG JAR_FILE=target/*.jar
       COPY ${JAR_FILE} app.jar
       ENTRYPOINT ["java","-jar","/app.jar"]
       
       -- myapp-deployment.yaml
       apiVersion: apps/v1
       kind: Deployment
       metadata:
         name: myapp
       spec:
         replicas: 3
         selector:
           matchLabels:
             app: myapp
         template:
           metadata:
             labels:
               app: myapp
           spec:
             containers:
               - name: myapp-web
                 image: dennis1945/myapp
                 ports:
                   - containerPort: 8080
                 env:
                   - name: PORT
                     value: "8080"
       ---
       apiVersion: v1
       kind: Service
       metadata:
         name: myapp
       spec:
         type: NodePort
         ports:
           - port: 80
             targetPort: 8080
         selector:
           app: myapp
       
       
