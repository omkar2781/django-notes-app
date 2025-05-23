@Library("shared") _
pipeline {
    agent {
        label 'vinod'
    }

    environment {
        IMAGE_NAME = 'notes-app'
    }

    stages { // Main stages block starts here

        stage("Hello") {
            steps {
                script {
                    hello() // Assuming 'hello()' is defined in your 'shared' library
                }
            }
        }

        stage('Clone Code') {
            steps {
                echo 'Cloning repository...'
                // Assuming 'git' step is preferred, or if 'clone' is a custom shared library step, use named parameters.
                // Example using the built-in 'git' step:
                git url: "https://github.com/omkar2781/django-notes-app.git", branch: "main"
                // If 'clone' is a custom shared library step, it should look like:
                // script {
                //     clone(url: "https://github.com/omkar2781/django-notes-app.git", branch: "main")
                // }
                echo 'Repository cloned.' // This was originally outside the steps block, moved it in.
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
                    // Assuming 'docker_built' takes named parameters like this. Adjust if different.
                    docker_built(imageName: "notes-app", tag: "latest", dockerUser: "omkar2781")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Assuming 'docker_push' takes named parameters like this. Adjust if different.
                    docker_push(imageName: "notes-app", tag: "latest", dockerUser: "omkar2781", credentialsId: "dockerhub")
                    // Note: If docker_push handles the login, you don't need 'withCredentials' here.
                    // If it expects credentials to be pre-logged in, the 'withCredentials' block is needed.
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
    } // Main stages block ends here
}
