pipeline {
    agent any

    environment {
        registry = "your-dockerhub-username/simple-node-app"
        registryCredential = 'docker-credentials'
        dockerImage = ''
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
        stage('Deploy Docker Container') {
            steps {
                script {
                    sh 'docker run -d -p 3000:3000 --name simple-node-app ${registry}'
                }
            }
        }
    }
    post {
        always {
            script {
                sh 'docker container rm -f simple-node-app || true'
            }
        }
    }
}
