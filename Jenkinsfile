@Library("shared") _
pipeline {
    agent {
        label 'vinod'
    }

    environment {
        IMAGE_NAME = 'notes-app'
    }

    stages {
        stage("Hello") {
            steps {
                script {
                    hello()
                }
            }
        }

        stage('Clone Code') {
            steps {
                echo 'Cloning repository...'
                git url: "https://github.com/omkar2781/django-notes-app.git", branch: "main"
                echo 'Repository cloned.'
            }
        }

        stage('Prepare .dockerignore') {
            steps {
                echo 'Creating .dockerignore to exclude sensitive files/folders...'
                sh '''
                    echo "data/" > .dockerignore
                    echo ".git" >> .dockerignore
                    echo "README.md" >> .dockerignore
                    echo "__pycache__/" >> .dockerignore
                    echo "*.pyc" >> .dockerignore
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // This is where 'docker_built' was changed to 'docker_build'
                    docker_build(imageName: "notes-app", tag: "latest", dockerUser: "omkar2781")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    docker_push(imageName: "notes-app", tag: "latest", dockerUser: "omkar2781", credentialsId: "dockerhub")
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application with Docker Compose...'
                sh '''
                    docker stop notes-app || true
                    docker rm notes-app || true

                    docker stop db_cont || true
                    docker rm db_cont || true

                    docker compose up -d
                '''
            }
        }
    }
}
