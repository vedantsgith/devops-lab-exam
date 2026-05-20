pipeline {
    agent any
    environment {
        AZURE_ACR = 'yourcollegeacr.azurecr.io'
        IMAGE_NAME = 'devops-exam-backend'
        TAG = "${env.BUILD_ID}"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --format XML', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeLocal') {
                    bat 'sonar-scanner.bat -Dsonar.projectKey=DevOpsExam -Dsonar.sources=.'
                }
            }
        }
        stage('Docker Build') {
            steps {
                bat "docker build -t ${AZURE_ACR}/${IMAGE_NAME}:${TAG} ."
            }
        }
        stage('Push to Azure') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'azure-acr-creds', passwordVariable: 'ACR_PWD', usernameVariable: 'ACR_USR')]) {
                    bat "docker login ${AZURE_ACR} -u %ACR_USR% -p %ACR_PWD%"
                    bat "docker push ${AZURE_ACR}/${IMAGE_NAME}:${TAG}"
                }
            }
        }
    }
}