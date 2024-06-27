pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/itsFarhanPatel/NewTask.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("simple-node-app")
                }
            }
        }
        stage('Deploy Docker Container') {
            steps {
                script {
                    docker.withRegistry('', 'docker-credentials') {
                        dockerImage.push('latest')
                    }
                    sh 'docker run -d -p 3000:3000 simple-node-app'
                }
            }
        }
    }
}

