pipeline {
    agent any

    environment {
        // Avoid hardcoded secrets, use Jenkins credentials plugin
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_id') // Set this in Jenkins
        IMAGE_NAME = "cloudchuck/myapplication"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    tools {
        maven 'maven-3.5.2'
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/CloudChuck/DevOps-Example.git'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${env.IMAGE_NAME}:${env.IMAGE_TAG}")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin"
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push image with tag and also tag as latest
                    sh "docker tag ${env.IMAGE_NAME}:${env.IMAGE_TAG} ${env.IMAGE_NAME}:latest"
                    sh "docker push ${env.IMAGE_NAME}:${env.IMAGE_TAG}"
                    sh "docker push ${env.IMAGE_NAME}:latest"
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
