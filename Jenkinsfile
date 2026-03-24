pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'http://sonarqube:9000'
        DOCKER_IMAGE = 'sudo503/java-app:latest'
    }

    stages {

        stage('Build - Java17') {
            steps {
                script {
                    docker.image('maven:3.9.9-eclipse-temurin-17').inside('--network cicd-network') {
                        sh 'mvn clean package -DskipTests'
                    }
                }
            }
        }

        stage('Test - Java11') {
            steps {
                script {
                    docker.image('maven:3.9.9-eclipse-temurin-11').inside('--network cicd-network') {
                        sh 'mvn test'
                    }
                }
            }
        }

        stage('SonarQube Analysis - Java17') {
            steps {
                script {
                    docker.image('maven:3.9.9-eclipse-temurin-17').inside('--network cicd-network') {
                        sh """
                        mvn sonar:sonar \
                        -Dsonar.host.url=$SONARQUBE_SERVER \
                        -Dsonar.login=squ_fd53f999b0b58cfd200eac78858c6dde26782a7d
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
