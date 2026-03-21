pipeline {
    agent any

    environment {
        DOCKER_HUB = "navesing2112"
        IMAGE_NAME = "train-app"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/nks2112/Abstergo_cicd.git',
                    credentialsId: 'git-token'
            }
        }

        stage('Build Image') {
            steps {
                bat 'docker build -t %DOCKER_HUB%/%IMAGE_NAME%:%BUILD_NUMBER% .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat 'echo %PASS% | docker login -u %USER% --password-stdin'
                    bat 'docker push %DOCKER_HUB%/%IMAGE_NAME%:%BUILD_NUMBER%'
                }
            }
        }

        // ✅ NEW STAGE ADDED HERE
        stage('Test Kubernetes') {
            steps {
                bat 'kubectl get nodes'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                powershell -Command "(Get-Content k8s/deployment.yaml) -replace 'IMAGE','%DOCKER_HUB%/%IMAGE_NAME%:%BUILD_NUMBER%' | Set-Content k8s/deployment.yaml"
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}
