# jenkins-docker-kubernetes
A CI/CD pipeline to deploy application on kubernetes cluster using jenkins \
\
Plugins needed on jenkins: \ 1. docker-pipeline \ 2. Kubernetes-cli \
```bash
#!/bin/bash
#######
# USING REPO - https://github.com/networknuts/jenkins-docker-kubernetes.git
#######

# Install Docker

echo "****************************"
echo ""
echo "*****Installing Docker******"
echo ""
echo "****************************"

sudo apt-get update -y
sudo apt-get install ca-certificates curl -y
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
sudo systemctl start docker
# Install kubectl
echo "****************************"
echo ""
echo "*****Installing Kubectl*****"
echo ""
echo "****************************"

sudo apt-get update -y
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update -y
sudo apt-get install -y kubectl

# Install Jenkins

echo "****************************"
echo ""
echo "*****Installing Jenkins*****"
echo ""
echo "****************************"

sudo apt-get update -y
sudo apt install fontconfig openjdk-17-jre -y
java -version
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status jenkins

echo "************************************"
echo ""
echo "**Add jenkins user in docker group**"
echo ""
echo "************************************"
sudo usermod -aG docker jenkins
echo ""
echo ""
echo "################################################"
echo "DON'T FORGET to add current user in docker group"
echo "################################################"
```
