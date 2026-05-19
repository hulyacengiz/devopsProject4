pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'cengizhulya/devops-project'
        DOCKER_TAG = 'latest'
        // Jenkins Credentials kısmında DockerHub bilgilerin için vereceğin ID:
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        KUBECONFIG = 'C:/Users/hulyacengiz/.kube/config'
    }

    tools {
        // Manage Jenkins > Tools altında kurduğumuz Maven ismi
        maven 'maven-3' 
    }

    stages {
        stage('Clone') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the JAR...'
                bat 'mvn clean package -DskipTests'
            }
        }
        
        stage('Docker Build') {
            steps {
                echo 'Building Docker Image...'
                bat "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }
        
        stage('Docker Login') {
            steps {
                echo 'Logging into DockerHub...'
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                }
            }
        }
        
        stage('Docker Push') {
            steps {
                echo 'Pushing image to DockerHub...'
                bat "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }
        
        stage('K8s Deploy') {
            steps {
                echo 'Deploying to Kubernetes...'
                // Windows'da minikube ile gelen kubectl çalışması için
                bat 'kubectl apply -f deployment.yaml'
                bat 'kubectl apply -f service.yaml'
            }
        }
    }
}
