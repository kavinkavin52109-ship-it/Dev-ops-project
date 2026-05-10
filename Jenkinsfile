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
stage('Terraform Setup') {
    steps {

        withCredentials([
            usernamePassword(
                credentialsId: 'aws-cred',
                usernameVariable: 'AWS_ACCESS_KEY_ID',
                passwordVariable: 'AWS_SECRET_ACCESS_KEY'
            )
        ]) {

            dir('terraform') {

                sh '''
                    export AWS_DEFAULT_REGION=ap-south-1

                    echo "Checking AWS Login..."
                    aws sts get-caller-identity

                    echo "Terraform Init..."
                    terraform init

                    echo "Terraform Apply..."
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
