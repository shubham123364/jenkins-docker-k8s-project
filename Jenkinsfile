pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'http://sonarqube:9000'
        DOCKER_IMAGE = 'YOUR_DOCKER_HUB_USERNAME/java-app:latest'
    }

    stages {

        stage('Build - Java17') {
            steps {
                script {
                    docker.image('eclipse-temurin:17').inside {
                        sh 'apt update && apt install -y maven'
                        sh 'mvn clean package -DskipTests'
                    }
                }
            }
        }

        stage('Test - Java11') {
            steps {
                script {
                    docker.image('eclipse-temurin:11').inside {
                        sh 'apt update && apt install -y maven'
                        sh 'mvn test'
                    }
                }
            }
        }

        stage('SonarQube Analysis - Java8') {
            steps {
                script {
                    docker.image('eclipse-temurin:8').inside {
                        sh 'apt update && apt install -y maven'
                        sh """
                        mvn sonar:sonar \
                        -Dsonar.host.url=$SONARQUBE_SERVER \
                        -Dsonar.login=YOUR_SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}
