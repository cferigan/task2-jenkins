pipeline {

    agent any

    stages {

        stage('Build') {

            steps {

                sh '''
                docker build -t cferigan/task2-jenkins flask-app
                docker build -t cferigan/task2-db db
                docker build -t cferigan/task2-nginx nginx
                
                '''

            }

        }
        
        stage('Push') {

            steps {

                sh '''
                docker push cferigan/task2-jenkins
                docker push cferigan/task2-db
                docker push cferigan/task2-nginx
                '''

            }

        }
          stage('Deploy') {

            steps {

                sh '''
                ssh jenkins@connor-deploy <<EOF
                docker network rm task2-net && echo "task2-net removed" || echo "network already removed"
                docker network create task2-net
                docker stop db && echo "Stopped db" || echo "db is not running"
                socker rm db && echo "removed db" || echo "db does not exist"
                docker stop nginx && echo "Stopped nginx" || echo "nginx is not running"
                docker rm nginx && echo "removed nginx" || echo "nginx does not exist"
                docker stop flask-app && echo "Stopped flask-app" || echo "flask-app is not running"
                docker rm flask-app && echo "removed flask-app" || echo "flask-app does not exist"
                docker run -d --name mysql --network task2-net cferigan/task2-db
                docker run -d -p 80:5000 --name flask-app --network task2-net cferigan/task2-jenkins
                docker run -d --name nginx --network task2-net -p 82:80 cferigan/task2-nginx
                '''

            }
 
        }

    }

}