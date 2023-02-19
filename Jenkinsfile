pipeline {
    agent any
    environment {  
        DOCKER_HUB_LOGIN = credentials('docker-hub')
    }
    stages {
        stage('Automation') {
            steps {
               sh 'git clone -b master https://github.com/javiergiuga/scrip-ninja-automation.git'
            }
        }
        stage('install dependencies') {
            agent{
                docker {
                    image 'node:erbium-alpine'
                    args '-u root:root'
                }
            }
            steps {
                echo "Init"
                sh 'npm install'
            }
        } 
        stage('Docker Build shopping') {
            steps {
               sh './scrip-ninja-automation/shopping-cart/docker_build.sh'
            }
        }
        stage('Docker Push shopping to Docker-hub') {
            steps {
                sh './scrip-ninja-automation/shopping-cart/docker_push.sh'
            }
        }
        stage('Deploy to EC2 shopping') {
            steps {
                sshagent(['ssh-ec2']){
                    sh './scrip-ninja-automation/shopping-cart/deploy_to_ec2_compose.sh'
                }
            }
        }
        stage('Docker Build products') {
            steps {
               sh './scrip-ninja-automation/products/docker_build.sh'
            }
        }
        stage('Docker Push products to Docker-hub') {
            steps {
                sh './scrip-ninja-automation/products/docker_push.sh'
            }
        }
        stage('Deploy to EC2 products') {
            steps {
                sshagent(['ssh-ec2']){
                    sh './scrip-ninja-automation/products/deploy_to_ec2_compose.sh'
                }
            }
        }
        stage('Docker Build frontend') {
            steps {
               sh './scrip-ninja-automation/frontend/docker_build.sh'
            }
        }
        stage('Docker Push frontend to Docker-hub') {
            steps {
                sh './scrip-ninja-automation/frontend/docker_push.sh'
            }
        }
        stage('Deploy to EC2 frontend') {
            steps {
                sshagent(['ssh-ec2']){
                    sh './scrip-ninja-automation/frontend/deploy_to_ec2_compose.sh'
                }
            }
        }
        stage('Notify Telegram') {
            steps {
                sh 'chmod +x scrip-ninja-automation/frontend/telegram.sh'
            }
        }

    } //--end stages

    // CLEAN WORKSPACES
    post { 
        always { 
            cleanWs()
        }
    }
}