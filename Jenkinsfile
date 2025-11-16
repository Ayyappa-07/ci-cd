pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'ayyuraj'
        DOCKERHUB_IMAGE = 'cicd-demo'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKERHUB_USER/$DOCKERHUB_IMAGE:latest ."
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'DOCKERHUB_PASS')]) {
                    sh "echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push $DOCKERHUB_USER/$DOCKERHUB_IMAGE:latest"
            }
        }

        stage('Deploy on EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@3.110.207.227 '
                        docker stop cicd-demo || true
                        docker rm cicd-demo || true
                        docker pull $DOCKERHUB_USER/$DOCKERHUB_IMAGE:latest
                        docker run -d --name cicd-demo -p 3000:3000 $DOCKERHUB_USER/$DOCKERHUB_IMAGE:latest
                    '
                    """
                }
            }
        }
    }
}
