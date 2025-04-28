pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('dockerhub_id') // DockerHub username/password from Jenkins credentials
        DOCKER_HUB_USER = "${DOCKER_HUB_CREDENTIALS_USR}"     // Dynamic Username
        DOCKER_HUB_PASS = "${DOCKER_HUB_CREDENTIALS_PSW}"
        REPO_NAME = "myapplication"                          // Repo name (you can make this dynamic too if you want)
        IMAGE_NAME = "${DOCKER_HUB_USER}/${REPO_NAME}"        // Full image path
        IMAGE_TAG = "${env.BUILD_NUMBER}"                    // Unique tag per build
    }

    tools {
        maven 'maven-3.5.2'
    }

    stages {
        stage('Clone Repository') {
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
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                sh "echo ${DOCKER_HUB_PASS} | docker login -u ${DOCKER_HUB_USER} --password-stdin"
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${IMAGE_NAME}:latest"
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
