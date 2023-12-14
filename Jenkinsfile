pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                        sh '''
                        kubectl create namespace prod || echo "Namespace prod already exists"
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        kubectl create namespace dev || echo "Namespace dev already exists"
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                        sh '''
                        docker build -t mikkydice/flask-jenk:latest -t mikkydice/flask-jenk:prod-v${BUILD_NUMBER} .
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        docker build -t mikkydice/flask-jenk:latest -t mikkydice/flask-jenk:dev-v${BUILD_NUMBER} .
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                        sh '''
                        docker push mikkydice/flask-jenk:latest
                        docker push mikkydice/flask-jenk:prod-v${BUILD_NUMBER}
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        docker push mikkydice/flask-jenk:latest
                        docker push mikkydice/flask-jenk:dev-v${BUILD_NUMBER}
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/main") {
                        sh '''
                        kubectl apply -n prod -f ./Kubernetes
                        kubectl set image deployment/flask-deployment task1=mikkydice/flask-jenk:prod-v${BUILD_NUMBER} -n prod
                        '''
                    } else if (env.GIT_BRANCH == "origin/dev") {
                        sh '''
                        kubectl apply -n dev -f ./Kubernetes
                        kubectl set image deployment/flask-deployment task1=mikkydice/flask-jenk:dev-v${BUILD_NUMBER} -n dev
                        '''
                    } else {
                        sh '''
                        echo "Branch not recognised"
                        '''
                    }
                }
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