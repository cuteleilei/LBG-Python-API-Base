pipeline {
    agent any
    stages {
         stage('Init') {
            steps {
                sh '''
                
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.5 << EOF
                docker network create jenk-network || echo "Network Already Exists"
                docker stop flask-app || echo "not running"
                docker stop nginxcontainer || echo "nginx Not Running"
                docker rm flask-app || echo "not running"
                docker rm nginxcontainer || echo "nginx Not Running"
                '''
           }
        }

         stage('Build') {
            steps {
                sh '''
                docker build -t cuteleilei/python-api -t cuteleilei/python-api:v${BUILD_NUMBER} .
                docker build -t cuteleilei/mynginx -t cuteleilei/mynginx:v${BUILD_NUMBER} ./nginx
                '''
           }
        }

        stage('Push') {
            steps {
                sh '''
                docker push cuteleilei/python-api 
                docker push cuteleilei/python-api:v${BUILD_NUMBER}
                docker push cuteleilei/mynginx:latest
                docker push cuteleilei/mynginx:v${BUILD_NUMBER}
                '''
           }
        }
        stage('Deploy') {
            steps {
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.5 << EOF
                docker run -d --name flask-app cuteleilei/python-api
                docker run -d -p 80:80 --name nginxcontainer --network jenk-network cuteleilei/mynginx
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
