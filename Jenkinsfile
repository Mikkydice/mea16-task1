pipeline {
    agent any
    stages {

        stage('Build') {
            steps {
                sh '''
                docker build -t mikkydice/flask-jenk:latest -t mikkydice/flask-jenk:v${BUILD_NUMBER} .
                '''
            }
        }

        stage('Push') {
            steps {
                sh '''
                docker push mikkydice/flask-jenk:latest
                docker push mikkydice/flask-jenk:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                kubectl apply -f ./kubernetes
                kubectl set image deployment/flask-deployment task1=mikkydice/flask-jenk:v${BUILD_NUMBER}
                '''
            }
        }

        stage('CleanUp') {
            steps {
                sh '''
                docker system prune -f
                '''
            }
        }
    }

}