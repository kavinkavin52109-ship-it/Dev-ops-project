pipeline {
    agent any

    environment {
        DOCKER_HUB = "kavinkavin52109"
        IMAGE_NAME = "dev-ops-project"
        TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                dir('app') {
                    sh '''
                        docker build -t $DOCKER_HUB/$IMAGE_NAME:$TAG .
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-login') {
                        sh '''
                            docker push $DOCKER_HUB/$IMAGE_NAME:$TAG
                        '''
                    }
                }
            }
        }

        stage('Debug PATH') {
            steps {
                sh '''
                    echo "PATH=$PATH"
                    which terraform || echo "terraform not found"
                '''
            }
        }

        stage('Terraform Setup') {
            steps {
                withCredentials([[
                    credentialsId: 'aws-cred',
                    accessKeyVariable: 'AKIAWZZBUALWMILTNBMR',
                    secretKeyVariable: '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'
                ]]) {
                    dir('terraform') {
                        sh '''
                         export AWS_DEFAULT_REGION=ap-south-1
                            aws sts get-caller-identity
                            terraform init
                            terraform apply -auto-approve
                        '''
                    }
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
