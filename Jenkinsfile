pipeline {
    agent any
    
    environment {
        DOCKER_CREDENTIALS_ID = 'docker_token' // Jenkins에 저장된 Docker Hub 자격증명 ID
        BACKEND_IMAGE = "rjsdn9025/awsb" // 백엔드 Docker 이미지 이름
        FRONTEND_IMAGE = "rjsdn9025/awsc" // 프론트엔드 Docker 이미지 이름
    }

    stages {
        stage('Checkout') {
            steps {
                // Git repository에서 코드 체크아웃
                git credentialsId: 'github-token', url: 'https://github.com/rjsdn9025/miniProject.git', branch: 'main'
            }
        }
        
        stage('Build Backend Docker Image') {
            steps {
                script {
                    // backend 디렉토리로 이동 후 Docker 이미지 빌드
                    dir('awsb') {
                        sh 'docker build -t $BACKEND_IMAGE .'
                    }
                }
            }
        }
        
        stage('Build Frontend Docker Image') {
            steps {
                script {
                    // frontend 디렉토리로 이동 후 Docker 이미지 빌드
                    dir('awsc') {
                        sh 'docker build -t $FRONTEND_IMAGE .'
                    }
                }
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                script {
                    // Docker Hub에 로그인
                    withCredentials([usernamePassword(credentialsId: "$DOCKER_CREDENTIALS_ID", usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    }
                }
            }
        }
        
        stage('Push Backend Docker Image') {
            steps {
                script {
                    // Docker Hub에 백엔드 이미지 푸시
                    sh 'docker push $BACKEND_IMAGE'
                }
            }
        }
        
        stage('Push Frontend Docker Image') {
            steps {
                script {
                    // Docker Hub에 프론트엔드 이미지 푸시
                    sh 'docker push $FRONTEND_IMAGE'
                }
            }
        }
    }

    post {
        always {
            // 항상 빌드가 끝난 후 Docker 로그아웃 수행
            sh 'docker logout'
        }
    }
}
