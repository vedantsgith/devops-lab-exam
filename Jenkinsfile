pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = 'vedant030' 
        IMAGE_NAME = 'devops-exam-backend'
        TAG = "${env.BUILD_ID}"
    }
    tools {
        nodejs 'Node 20'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('OWASP Dependency-Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format XML', odcInstallation: 'OWASP'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
        stage('SonarQube Analysis') {
            environment {
                SCANNER_HOME = tool 'SonarScanner'
            }
            steps {
                withSonarQubeEnv('SonarQube') {
                    bat '"%SCANNER_HOME%\\bin\\sonar-scanner.bat"'
                }
            }
        }
        stage('Docker Build') {
            steps {
                // Notice the tag now includes your Docker Hub username
                bat "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${TAG} ."
                bat "docker tag ${DOCKER_HUB_USER}/${IMAGE_NAME}:${TAG} ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', passwordVariable: 'DOCKER_PWD', usernameVariable: 'DOCKER_USR')]) {
                    bat "docker login -u %DOCKER_USR% -p %DOCKER_PWD%"
                    bat "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${TAG}"
                    bat "docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest"
                }
            }
        }
    }
}
