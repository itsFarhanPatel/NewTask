pipeline {
    agent any
    environment {
        // EC2_IP = '54.176.206.128'
        // SSH_CREDENTIALS_ID = 'ec2-ssh-credentials'
        registry = "docker.io"
        dockerImage = "itsfarhanpatel/myimg"
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

       stage('Deploy Docker Container') {
            steps {
                    sh 'docker run -d -p 3000:3000 itsfarhanpatel/myimg '
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
