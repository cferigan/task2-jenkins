pipeline {

    agent any
    environment {
        YOUR_NAME = credentials("YOUR_NAME")
    }

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
                export MYSQL_ROOT_PASSWORD=${YOUR_NAME}
                docker network rm task2-net && echo "task2-net removed" || echo "network already removed"
                docker network create task2-net
                docker stop flask-db && echo "Stopped db" || echo "db is not running"
                docker rm flask-db && echo "removed db" || echo "db does not exist"
                docker stop nginx && echo "Stopped nginx" || echo "nginx is not running"
                docker rm nginx && echo "removed nginx" || echo "nginx does not exist"
                docker stop flask-app && echo "Stopped flask-app" || echo "flask-app is not running"
                docker rm flask-app && echo "removed flask-app" || echo "flask-app does not exist"
                docker run -d --name flask-db --network task2-net -e MYSQL_ROOT_PASSWORD=YOUR_NAME=${YOUR_NAME} cferigan/task2-db
                docker run -d -p 80:5000 --name flask-app --network task2-net -e MYSQL_ROOT_PASSWORD=YOUR_NAME=${YOUR_NAME} cferigan/task2-jenkins
                docker run -d --name nginx --network task2-net -p 82:80 cferigan/task2-nginx
                '''

            }
 
        }

    }

}