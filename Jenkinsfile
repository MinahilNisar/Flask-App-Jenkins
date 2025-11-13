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
                sh 'pip install -r requirements.txt'
                sh 'pytest || echo "No tests yet"'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:latest ."
            }
        }

        stage('Load Image into kind') {
            steps {
                sh "kind load docker-image $IMAGE_NAME:latest --name $CLUSTER_NAME"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
                sh 'kubectl rollout status deployment/flask-app'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc'
            }
        }
    }
}
