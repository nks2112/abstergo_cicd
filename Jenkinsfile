pipeline {
    agent any

    environment {
        DOCKER_HUB = "navesing2112"
        IMAGE_NAME = "train-app"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/nks2112/Abstergo_cicd.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $DOCKER_HUB/$IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'navesing2112', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_HUB/$IMAGE_NAME:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                sed -i "s|IMAGE|$DOCKER_HUB/$IMAGE_NAME:$BUILD_NUMBER|g" k8s/deployment.yaml
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }
}
