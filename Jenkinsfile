pipeline {

    agent any
    environment {
        MYSQL_ROOT_PASSWORD = credentials("YOUR_NAME")
    }

    stages {

        stage('Build') {

            steps {

                sh '''
                docker build -t cferigan/task2-jenkins flask-app
                docker build -t cferigan/task2-db db
                
                '''

            }

        }
        
        stage('Push') {

            steps {

                sh '''
                docker push cferigan/task2-jenkins
                docker push cferigan/task2-db
                '''

            }

        }
          stage('Deploy') {

            steps {

                sh '''
                sed -e 's,{{password}},'${MYSQL_ROOT_PASSOWRD}',g;' db-password.yaml | kubectl apply -f -
                kubectl apply -f task2-manifest.yaml
                kubectl apply -f nginx-config.yaml
                kubectl apply -f nginx-pod.yaml

                '''

            }
 
        }

    }

}