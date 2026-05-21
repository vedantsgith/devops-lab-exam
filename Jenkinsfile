pipeline {
    agent any
    environment {
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
                bat "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }
    }
}
