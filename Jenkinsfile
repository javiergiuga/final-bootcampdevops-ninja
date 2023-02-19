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
        stage('Unit Test') {
            agent{
                docker {
                    image 'node:erbium-alpine'
                    args '-u root:root'
                }
            }
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh ' cd frontend && npm run test'
                }
            }
        } 
        stage('Docker Build & Push Shopping-Cart') {
            steps {
               sh './scrip-ninja-automation/shopping-cart/docker_build.sh'
               sh './scrip-ninja-automation/shopping-cart/docker_push.sh'
            }
        }
        stage('Docker Build & Push Products') {
            steps {
               sh './scrip-ninja-automation/products/docker_build.sh'
               sh './scrip-ninja-automation/products/docker_push.sh'
            }
        }
        stage('Docker Build & Push Frontend') {
            steps {
               sh './scrip-ninja-automation/frontend/docker_build.sh'
               sh './scrip-ninja-automation/frontend/docker_push.sh'
            }
        }
        stage('Deploy to EC2') {
            steps {
                sshagent(['ssh-ec2']){
                    sh './scrip-ninja-automation/deploy_to_ec2_compose.sh'
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