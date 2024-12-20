pipeline {
    agent any
    parameters {
        string(name: 'DOCKER_TAG', description: 'Enter the tag for the image', defaultValue: 'latest')
    }
    stages {
        stage('Git Checkout') {
            steps {
                // This stage is to get the Dockerfile
                git branch: 'main',
                    url: 'https://github.com/networknuts/jenkins-docker-kubernetes.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Make sure that Docker is installed and configured before reaching here
                    def dockerImage = docker.build("docker.io/alokaryan/kube-app:${params.DOCKER_TAG}","-f Dockerfile .")
                    docker.withRegistry('','docker-creds') {
                        dockerImage.push("${params.DOCKER_TAG}")
                    }
                }
            }
        }
        stage('Scan Docker Image') {
            steps {
                sh "docker run -v /var/run/docker.sock:/var/run/docker.sock -v $HOME/Library/Caches:/root/.cache/ aquasec/trivy:0.51.1 image docker.io/alokaryan/kube-app:${params.DOCKER_TAG}"
            }
        }
        stage("Update Tag in deployment.yml") {
            steps {
                script {
                    sh "sed -i 's|image:.*|image: docker.io/alokaryan/kube-app:${params.DOCKER_TAG}|g' deployment.yaml"
                }
            }
        }
        stage("Deploy on Kubernetes") {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubernetes-creds', serverUrl: 'https://10.0.0.100:6443']) {
                        sh "kubectl apply -f deployment.yaml"
                    }
                }
            }
        }
    }
}
