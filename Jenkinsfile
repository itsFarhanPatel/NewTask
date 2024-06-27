pipeline {
    agent any
    environment {
        EC2_IP = '54.176.206.128'
        SSH_CREDENTIALS_ID = 'ec2-ssh-credentials'
        registry = "docker.io"
        dockerImage = "itsfarhanpatel/new-img"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/itsFarhanPatel/NewTask.git'
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}/${dockerImage}")
                }
            }
        }

        stage('Push Docker image') {
            steps {
                script {
                    docker.withRegistry('', 'docker-credentials') {
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    sshagent(credentials: [ssh-credentials-id]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} 'bash -s' < deploy-script.sh
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}

