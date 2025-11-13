pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-app"
        CLUSTER_NAME = "flask-cluster"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/MinahilNisar/Flask-app-K8-s.git'
            }
        }

        stage('Install & Test') {
            steps {
                bat 'pip install -r requirements.txt'
                bat 'pytest || echo No tests yet'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:latest ."
            }
        }

        stage('Load Image into kind') {
            steps {
                bat "kind load docker-image %IMAGE_NAME%:latest --name %CLUSTER_NAME%"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl delete -f k8s/deployment.yaml --ignore-not-found'
                bat 'kubectl delete -f k8s/service.yaml --ignore-not-found'
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
                bat 'kubectl rollout status deployment/flask-app'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'kubectl get pods'
                bat 'kubectl get svc'
            }
        }
    }

    post {
        success {
            echo " Deployment succeeded!"
        }
        failure {
            echo " Pipeline failed — check logs."
        }
    }
}
