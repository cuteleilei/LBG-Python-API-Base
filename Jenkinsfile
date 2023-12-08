pipeline {
    agent any
    stages {
         stage('Init') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.5 << EOF
                docker stop flask-app || echo "not running"
                docker rm flask-app || echo "not running"
                '''
           }
        }

         stage('Build') {
            steps {
                sh '''
                docker build -t cuteleilei/python-api -t cuteleilei/python-api:v${VERSION_NUMBER}
                '''
           }
        }

        stage('Push') {
            steps {
                sh '''
                docker push -t cuteleilei/python-api -t cuteleilei/python-api:v${VERSION_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.5 << EOF
                docker run -d -p 80:8080 --name flask-app cuteleilei/python-api
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
