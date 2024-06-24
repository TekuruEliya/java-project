pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        DOCKERHUB_USERNAME = "${env.DOCKERHUB_USERNAME}"
        DOCKERHUB_PASSWORD = "${env.DOCKERHUB_PASSWORD}"
        IMAGE_NAME = "${env.IMAGE_NAME}"
        APP_NAME = "${env.APP_NAME}"
        RELEASE_VERSION = "{env.RELEASE_VERSION}"
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/TekuruEliya/java-project.git'   }
        }
        stage('Test') {
            steps {
                sh "mvn clean test package"
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t $IMAGE_NAME:latest .'
                }
            }
        }
        
        stage('Docker Image Scan using Trivy') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html $IMAGE_NAME"
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Log in to Docker Hub
                    sh """
                        echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
                    """
                    
                    // Push Docker image to Docker Hub
                    sh 'docker push "${IMAGE_NAME}"'
                }
            }
        }
    }
}
