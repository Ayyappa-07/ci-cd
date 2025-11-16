pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'ayyuraj'
        DOCKERHUB_IMAGE = 'cicd-demo'
    }

    stages {
        stage('Build & Deploy on EC2') {
            steps {
                // Use your SSH credentials configured in Jenkins
                sshagent(['ec2-ssh-key']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ubuntu@3.110.207.227 '
                        # Clone the repo or pull latest changes
                        if [ ! -d /tmp/ci-cd ]; then
                            git clone -b main https://github.com/Ayyappa-07/ci-cd.git /tmp/ci-cd
                        else
                            cd /tmp/ci-cd
                            git reset --hard
                            git pull
                        fi

                        # Build Docker image
                        docker build -t $DOCKERHUB_USER/$DOCKERHUB_IMAGE:latest /tmp/ci-cd

                        # Stop and remove old container if exists
                        docker stop $DOCKERHUB_IMAGE || true
                        docker rm $DOCKERHUB_IMAGE || true

                        # Run new container
                        docker run -d --name $DOCKERHUB_IMAGE -p 3000:3000 $DOCKERHUB_USER/$DOCKERHUB_IMAGE:latest
                    '
                    """
                }
            }
        }
    }
}
g