pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'master', credentialsId: 'git-credentials', url: 'https://github.com/itsFarhanPatel/NewTask.git'
            }
        }   
         stage('Build & Tag Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-credentials', toolName: 'docker') {
                            sh "docker build -t itsfarhanpatel/new:latest ."
                    }
               }
            }
        }
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker-credentials', toolName: 'docker') {
                            sh "docker push itsfarhanpatel/new:latest"
                    }
               }
            }
        }
        stage('Deploy to EC2') {
            steps {
                script {
                    // Login to Docker Hub using --password-stdin
                    withCredentials([usernamePassword(credentialsId: 'docker-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }

                    // Push the Docker image to Docker Hub
                    docker.withRegistry('', 'docker-credentials') {
                        dockerImage.push('latest')
                    }

                    // Deploy to EC2 instance
                    sshagent(['ssh-credentials-id']) {
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.176.206.128 docker run -d -p 3000:3000 itsfarhanpatel/new:latest'
                    }
                }
            }
        }
    }
}
