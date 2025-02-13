pipeline {
    agent { label 'docker-agent' }

    environment {
        DOCKER_IMAGE = 'sample-react-app'
        SERVER_IP = '192.9.200.245'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-repo/sample-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker tag $DOCKER_IMAGE your-dockerhub-username/$DOCKER_IMAGE:latest'
                    sh 'docker push your-dockerhub-username/$DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                sh '''
                ssh user@$SERVER_IP "docker pull your-dockerhub-username/$DOCKER_IMAGE:latest && \
                    docker stop react-container || true && \
                    docker rm react-container || true && \
                    docker run -d -p 80:3000 --name react-container your-dockerhub-username/$DOCKER_IMAGE:latest"
                '''
            }
        }
    }
}
