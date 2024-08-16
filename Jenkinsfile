pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'nodejs16'
    }
    environment {
        SCANNER_HOME = tool 'sonar scanner'
        APP_NAME = "reddit-clone-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "zhengqinghong"
        DOCKER_PASS = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Github') {
            steps {
                git branch: 'main', url: 'https://github.com/zqhgithubuser/a-reddit-clone.git'
            }
        }
        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv('SonarQube Server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName="Reddit Clone CI" \
                    -Dsonar.projectKey="Reddit Clone CI"'''
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
             }
        }
    }
}
