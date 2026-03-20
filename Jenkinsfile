pipeline {
    agent any
    environment {
        SONAR_TOKEN = credentials('sonar-token')
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
        // Automatically formats your Docker Hub image name
        DOCKER_IMAGE = "${DOCKERHUB_CREDS_USR}/emailservice" 
        SONAR_ORG = "Philip-Lucky" 
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('SonarCloud Code Scan') {
            steps {
                dir('src/emailservice') {
                    sh """
                    wget -q https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.0.1.3006-linux.zip
                    unzip -q sonar-scanner-cli-5.0.1.3006-linux.zip
                    ./sonar-scanner-5.0.1.3006-linux/bin/sonar-scanner \
                      -Dsonar.projectKey=${SONAR_ORG}_emailservice \
                      -Dsonar.organization=${SONAR_ORG} \
                      -Dsonar.host.url=https://sonarcloud.io \
                      -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                dir('src/emailservice') {
                    sh "docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} ."
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh "echo ${DOCKERHUB_CREDS_PSW} | docker login -u ${DOCKERHUB_CREDS_USR} --password-stdin"
                sh "docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}"
            }
        }
    }
}
