pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = "mmkhan1"
        IMAGE_NAME = "python-student-app"
        KUBE_CONFIG = credentials('kubeconfig-creds')  
    }

    triggers {
        githubPush()     // Auto-trigger on git push
    }

    stages {
        
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/mmkhan1/student-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKERHUB_USERNAME/$IMAGE_NAME:latest .'
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    sh "echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin"
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    sh "docker push $DOCKERHUB_USERNAME/$IMAGE_NAME:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig-creds', variable: 'KUBECONFIG_FILE')]) {
                        sh 'mkdir -p $HOME/.kube'
                        sh 'cp $KUBECONFIG_FILE $HOME/.kube/config'

                        sh "kubectl apply -f k8s-deployment.yaml"
                        sh "kubectl apply -f k8s-service.yaml"
                    }
                }
            }
        }
    }
}
