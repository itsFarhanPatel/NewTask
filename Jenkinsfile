pipeline {
    agent any

    environment {
        registry = "itsfarhanpatel/simple-node-app"
        registryCredential = 'docker-credentials'
        dockerImage = ''
        ec2Address = '54.176.206.128'
        ec2User = 'ubuntu'
        sshCredential = 'ssh-credentials-id'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/itsFarhanPatel/NewTask.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy Docker Container on EC2') {
            steps {
                script {
                    sshagent([sshCredential]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ${ec2User}@${ec2Address} << EOF
                        docker pull ${registry}
                        docker rm -f simple-node-app || true
                        docker run -d -p 3000:3000 --name simple-node-app ${registry}
                        EOF
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            script {
                echo 'Cleaning up Docker containers'
                sh """
                ssh -o StrictHostKeyChecking=no ${ec2User}@${ec2Address} << EOF
                docker container rm -f simple-node-app || true
                EOF
                """
            }
        }
    }
}
