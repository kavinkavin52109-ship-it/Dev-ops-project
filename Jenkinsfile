pipeline {
    agent any

    environment {
        DOCKER_HUB = "kavinkavin52109"
        IMAGE_NAME = "dev-ops-project"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/YOUR_USERNAME/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    sh '''
                        docker build -t $DOCKER_HUB/$IMAGE_NAME:latest .
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'dockerhub-creds', url: '']) {
                    sh '''
                        docker push $DOCKER_HUB/$IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Terraform Infrastructure Setup') {
            steps {
                dir('terraform') {
                    sh '''
                        terraform init
                        terraform apply -auto-approve
                    '''
                }
            }
        }

        stage('Kubernetes Deployment') {
            steps {
                dir('kubernetes') {
                    sh '''
                        kubectl apply -f deployment.yaml
                        kubectl apply -f service.yaml
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    kubectl get pods
                    kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Full DevOps pipeline deployed successfully!'
        }

        failure {
            echo 'FAILED: Check Jenkins console logs.'
        }
    }
}
