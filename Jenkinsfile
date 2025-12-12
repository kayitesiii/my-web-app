

pipeline {
    agent any

    environment {
        // Keeps your image name consistent across all stages
        DOCKER_IMAGE = 'chartinee/my-web-app'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Cloning the repository if it doesn't exist..."
                bat '''
                if not exist ".git" (
                    git clone https://github.com/kayitesiii/my-web-app.git .
                ) else (
                    echo Repository already exists. Skipping clone.
                    git fetch --all
                    git reset --hard origin/main
                )
                '''
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                bat 'dir'   // Windows agent
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                // Add actual test commands here later (e.g., npm test)
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying..."
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    // We stick with your TCP config if that is how your Docker Desktop is set up
                    docker.withServer('tcp://localhost:2375') {
                        // Notice I added the tag to the build command to be safe
                        def dockerImage = docker.build("${DOCKER_IMAGE}:latest", "--no-cache .")
                        
                        docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                            dockerImage.push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy to Local Docker Host') {
            steps {
                // FIXED: Used %DOCKER_IMAGE% variable instead of "username/..."
                // Added logic to stop the container only if it is actually running to prevent errors
                bat """
                    docker stop my-web-app || echo "Container not running..."
                    docker rm -f my-web-app || echo "No container to remove..."
                    docker run -d --name my-web-app -p 8090:3000 ${DOCKER_IMAGE}:latest
                """
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed Check logs."
        }
    }
}
