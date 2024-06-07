pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        // KUBECONFIG_CREDENTIALS = credentials('kubeconfig')
        AWS_CREDENTIALS = credentials('aws-credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/manishsonawane1998/devops-fullstack-task.git'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    docker.build('manishsonawane1998/backend:latest', '-f backend/Dockerfile .').push()
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    docker.build('manishsonawane1998/frontend:latest', '-f frontend/Dockerfile .').push()
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                    sh '''
                    cd infrastructure
                    terraform init
                    terraform apply -auto-approve
                    '''
                }
            }
        }

        // stage('Deploy to Kubernetes') {
        //     steps {
        //         withKubeConfig(credentialsId: 'kubeconfig') {
        //             sh '''
        //             kubectl apply -f k8s
        //             '''
        //         }
        //     }
        // }
    }

    post {
        always {
            cleanWs()
        }
    }
}

