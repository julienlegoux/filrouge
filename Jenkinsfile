pipeline {
    agent any
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    tools {
        nodejs 'NodeJS-24.11'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build & Deploy App') {
            steps {
                script {
                    dir('app') {
                        sh '''
                            docker build -t filrouge-app:${IMAGE_TAG} .
                            docker stop filrouge-app || true
                            docker rm filrouge-app || true
                            docker run -d \
                                --name filrouge-app \
                                --restart unless-stopped \
                                -p 3000:3000 \
                                filrouge-app:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
        
        stage('Build & Deploy Site') {
            steps {
                script {
                    dir('site') {
                        sh '''
                            docker build -t filrouge-site:${IMAGE_TAG} .
                            docker stop filrouge-site || true
                            docker rm filrouge-site || true
                            docker run -d \
                                --name filrouge-site \
                                --restart unless-stopped \
                                -p 3001:3000 \
                                filrouge-site:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
        
        stage('Build & Deploy Back') {
            steps {
                script {
                    dir('back') {
                        sh '''
                            docker build -t filrouge-back:${IMAGE_TAG} .
                            docker stop filrouge-back || true
                            docker rm filrouge-back || true
                            docker run -d \
                                --name filrouge-back \
                                --restart unless-stopped \
                                -p 8083:8083 \
                                filrouge-back:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
        
        stage('Cleanup Old Images') {
            steps {
                sh '''
                    docker image prune -f
                '''
            }
        }
    }
    
    post {
        success {
            echo '✅ Deployment successful! Services running on:'
            echo '   - App:  https://app.julienlegoux.cloud'
            echo '   - Site: https://site.julienlegoux.cloud'
            echo '   - API:  https://api.julienlegoux.cloud'
        }
        failure {
            echo '❌ Deployment failed! Check logs above.'
        }
    }
}