pipeline {
    agent { label 'docker-agent' }   

    environment {
        SERVER_IP = "192.9.200.245"
        SERVER_USER = "ptpl"
        SERVER_PASSWORD = "ptpl"
        REPO_URL = "https://github.com/MariaGillVarghese/reactapp.git"  
        APP_NAME = "app" 
        IMAGE_NAME = "my-app-image"  
        CONTAINER_NAME = "my-app-container"
        SONAR_HOST_URL = "http://192.9.200.245:80"
        SONAR_PROJECT_KEY = "my-react-app"
        SONAR_TOKEN = "squ_334e3305392526bb1756ac02e5eef6ed94270ab8"
    }

    stages {
        stage('Clone Repository') {
            steps {
                script {
                    sshCommand remote: [
                        name: 'remote-server',
                        host: SERVER_IP, 
                        user: SERVER_USER, 
                        password: SERVER_PASSWORD,
                        allowAnyHosts: true
                    ], command: """
                        rm -rf ${APP_NAME}  # Remove old repo if exists
                        git clone ${REPO_URL} ${APP_NAME} 
                    """
                }
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                script {
                    sshCommand remote: [
                        name: 'remote-server',
                        host: SERVER_IP, 
                        user: SERVER_USER, 
                        password: SERVER_PASSWORD,
                        allowAnyHosts: true
                    ], command: """
                        docker run --rm \
                            -v \$(pwd)/${APP_NAME}:/usr/src \
                            sonarsource/sonar-scanner-cli \
                            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                            -Dsonar.sources=/usr/src \
                            -Dsonar.host.url=${SONAR_HOST_URL} \
                            -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sshCommand remote: [
                        name: 'remote-server',
                        host: SERVER_IP, 
                        user: SERVER_USER, 
                        password: SERVER_PASSWORD,
                        allowAnyHosts: true
                    ], command: """
                        cd ${APP_NAME}
                        docker build -t ${IMAGE_NAME} .
                    """
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    sshCommand remote: [
                        name: 'remote-server',
                        host: SERVER_IP, 
                        user: SERVER_USER, 
                        password: SERVER_PASSWORD,
                        allowAnyHosts: true
                    ], command: """
                        docker stop ${CONTAINER_NAME} || true  # Stop container if running
                        docker rm ${CONTAINER_NAME} || true   # Remove container if exists
                        docker run -d --name ${CONTAINER_NAME} -p 3000:3000 ${IMAGE_NAME}
                    """
                }
            }
        }
    }
}
