pipeline {
    agent any
    stages {
         stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main'){
                        sh '''
                        ssh -i ~/.ssh/id_rsa jenkins@10.200.0.5 << EOF
                        docker network create jenk-network || echo "Network Already Exists"
                        docker stop flask-app || echo "not running"
                        docker stop nginxcontainer || echo "nginx Not Running"
                        docker rm flask-app || echo "not running"                
                        docker rm nginxcontainer || echo "nginx Not Running"                
                        docker rmi cuteleilei/python-api || echo "nginx Not Running"                
                        docker rmi cuteleilei/mynginx || echo "nginx Not Running"                
                        '''
                } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        ssh -i ~/.ssh/id_rsa jenkins@10.200.0.17 << EOF
                        docker network create jenk-network || echo "Network Already Exists"
                        docker stop flask-app || echo "not running"
                        docker stop nginxcontainer || echo "nginx Not Running"
                        docker rm flask-app || echo "not running"                
                        docker rm nginxcontainer || echo "nginx Not Running"                
                        docker rmi cuteleilei/python-api || echo "nginx Not Running"                
                        docker rmi cuteleilei/mynginx || echo "nginx Not Running"                
                        '''

                } else {
                    sh '''
                    echo "not recognized branch"
                    '''
                }

                }
                
           }
        }

         stage('Build') {
            steps {
                script { if (env.GIT_BRANCH == 'origin/main'){
                    
                    sh '''
                    echo "Build not required in main"
                        '''
                } else if (env.GIT_BRANCH == 'origin/dev'){
                    sh '''
                docker build -t cuteleilei/python-api -t cuteleilei/python-api:v${BUILD_NUMBER} .
                docker build -t cuteleilei/mynginx -t cuteleilei/mynginx:v${BUILD_NUMBER} ./nginx
                '''

                } else {
                    sh '''
                    echo "not recognized branch"
                    '''
                }

                }
                
           }
        }

        stage('Push') {
            steps {
                script { if (env.GIT_BRANCH == 'origin/main'){
                    sh '''
                        echo "Push not required in main"
                        '''

                } else if (env.GIT_BRANCH == 'origin/dev') {
                    sh '''
                docker push cuteleilei/python-api 
                docker push cuteleilei/python-api:v${BUILD_NUMBER}
                docker push cuteleilei/mynginx:latest
                docker push cuteleilei/mynginx:v${BUILD_NUMBER}
                '''

                } else {
                     sh '''
                        echo "Unrecognised branch"
                        '''

                }
           }
        }
        }
        stage('Deploy') {
            steps { 
                script { if (env.GIT_BRANCH == 'origin/main'){
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.5 << EOF
                docker run -d --name flask-app --network jenk-network  cuteleilei/python-api
                docker run -d -p 80:80 --name nginxcontainer --network jenk-network cuteleilei/mynginx
                '''
            } else if (env.GIT_BRANCH == 'origin/dev'){
                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.200.0.17 << EOF
                docker run -d --name flask-app --network jenk-network  cuteleilei/python-api
                docker run -d -p 80:80 --name nginxcontainer --network jenk-network cuteleilei/mynginx
                '''

            } else {

                sh '''
                        echo "Unrecognised branch"
                        '''
                
            }
                }
                
            }
        }

        stage('CleanUp') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/dev'){
                        sh '''
                        docker rmi cuteleilei/python-api           
                        docker rmi cuteleilei/mynginx  
                        '''
                    }

                }

                  sh '''
                docker system prune -f 
                '''
              
            }
        }
    }
}
