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
                    hello() // Assuming 'hello()' is defined in your 'shared' library and is approved
                }
            }
        }

        stage('Clone Code') {
            steps {
                echo 'Cloning repository...'
                // Using the built-in 'git' step directly, as it's typically sufficient
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
                    // Calling docker_build with positional arguments as per your shared library definition
                    docker_build("notes-app", "latest", "omkar2781")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Calling docker_push with positional arguments as per your shared library definition
                    // The order must match: Project, ImageTag, dockerhubUser, dockerhubcred
                    docker_push("notes-app", "latest", "omkar2781", "dockerhub")
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application with Docker Compose...'
                // Using the custom docker_compose shared library step
                script {
                    docker_compose()
                }
            }
        }
    }
}
