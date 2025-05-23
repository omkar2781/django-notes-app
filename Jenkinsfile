@Library("shared") _ // Ensure this is @Library, not @Liabrary
pipeline {
    agent {
        label 'vinod'    // This is the *only* thing that should be directly in the agent block for a 'label' agent.
    }

    environment {
        IMAGE_NAME = 'notes-app'
    }

    stages { // This is the main 'stages' block for the entire pipeline
        stage("Hello") { // Your "Hello" stage, now correctly placed
            steps {
                script {
                    hello() // Assuming 'hello()' is defined in your 'shared' library
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
                echo 'Building Docker image...'
                sh '''
                    docker build -t $IMAGE_NAME:latest .
                '''
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'dockerHubUser', passwordVariable: 'dockerHubPass')]) {
                    sh '''
                        echo "$dockerHubPass" | docker login -u "$dockerHubUser" --password-stdin
                        docker tag $IMAGE_NAME:latest $dockerHubUser/$IMAGE_NAME:latest
                        docker push $dockerHubUser/$IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application with Docker Compose...'
                sh '''
                    # Stop and remove the notes-app container (if defined in Dockerfile or directly run)
                    docker stop notes-app || true
                    docker rm notes-app || true

                    # --- ADDED THESE LINES TO CLEAN UP db_cont ---
                    docker stop db_cont || true
                    docker rm db_cont || true
                    # --- END ADDED LINES ---

                    docker compose up -d
                '''
            }
        }
    }
}
