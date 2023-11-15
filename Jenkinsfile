def appImage

def dbImage

pipeline {

    agent any

    environment {

        // Use your DockerHub username

        dockerUserName="cferigan"

        // Names of your Docker images

        imageNameApp = "task2-app"

        imageNameDb = "task2-db"

        // Constructed registry paths

        registryApp = "${dockerUserName}/${imageNameApp}"

        registryDb = "${dockerUserName}/${imageNameDb}"

        registryCredentials = 'dockerhub'

        secret_var = credentials('PasSword123')

    }

    stages {

        stage('Init') {

steps {

                script {

                    sh 'docker rm -f $(docker ps -qa) || true'

                    sh 'docker network create trio-task-network || true'

                    sh 'docker volume create new-volume'

                }

            }

        }

        stage('Build and Run Containers') {

            steps {

                script {

                    // Build the custom images

                    appImage = docker.build("${registryApp}:${env.BUILD_NUMBER}", "flask-app")

                    dbImage = docker.build("${registryDb}:${env.BUILD_NUMBER}", "db")

                    // Run containers

                    dbImage.run("-d --name mysql --network trio-task-network -v new-volume:/var/lib/mysql")

                    appImage.run("-d -e MYSQL_ROOT_PASSWORD=${secret_var} --name flask-app --network trio-task-network")

                    // Use standard Nginx image and bind mount for custom configuration

                    docker.image('nginx:latest').run("-d --name nginx -p 80:80 --network trio-task-network -v \$(pwd)/nginx/nginx.conf:/etc/nginx/nginx.conf")

                }

            }

        }

        stage('Push Images to DockerHub') {

            steps {

                script {

                    docker.withRegistry('', registryCredentials) {

                        appImage.push("${env.BUILD_NUMBER}")

                        appImage.push("latest")

                        dbImage.push("${env.BUILD_NUMBER}")

                        dbImage.push("latest")

                    }

                }

            }

        }

    }

}