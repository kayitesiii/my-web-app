// Use a declarative pipeline structure
pipeline {
    // Defines where the pipeline will run (your Jenkins Agent/Node)
    agent any

    // Environment variables used throughout the pipeline
    environment {
        // Image name for Docker Hub
        DOCKER_IMAGE = 'chartinee/my-web-app'
        // Credential ID configured in Jenkins for Docker Hub access
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'
        // Explicitly set DOCKER_HOST for Windows agent to communicate with Docker Desktop
        DOCKER_HOST = 'tcp://localhost:2375'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out code using Jenkins built-in Git plugin..."
                // **FIX:** This uses the Jenkins SCM capability, which already knows the full path to git.exe,
                // and correctly handles fetching/resetting the code from the repository defined in the job configuration.
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo "Listing files to confirm checkout location..."
                // Use the correct command for your Windows agent
                bat 'dir'
                echo "Build and Test steps would run here (e.g., npm install, npm test)..."
                // Placeholder for actual build/test commands
                // bat 'npm install'
                // bat 'npm test'
            }
        }

        stage('Build & Push Docker Image') {
            // Ensure Docker commands run only if the image build/test was successful
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                script {
                    echo "Building and pushing Docker image: ${DOCKER_IMAGE}:latest"

                    // 1. Build the image locally
                    // Use withServer for DOCKER_HOST setting, which is specified in the environment block
                    docker.withServer(env.DOCKER_HOST) {
                        def dockerImage = docker.build("${DOCKER_IMAGE}:latest", ".")

                        // 2. Push the image to Docker Hub using stored credentials
                        docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                            dockerImage.push('latest')
                            echo "Image pushed successfully to Docker Hub."
                        }
                    }
                }
            }
        }

        stage('Deploy to Local Docker Host') {
            steps {
                // Deployment commands using the Windows Batch shell (bat)
                bat """
                    echo Stopping and removing old container...
                    // Attempt to stop and remove, using '|| echo' to prevent failure if container doesn't exist
                    docker stop my-web-app || echo "Container 'my-web-app' not running, skipping stop."
                    docker rm -f my-web-app || echo "Container 'my-web-app' not found, skipping removal."

                    echo Running new container...
                    // Run the newly pushed image
                    docker run -d --name my-web-app -p 8090:3000 ${DOCKER_IMAGE}:latest
                    echo Deployment complete. Access app at http://<Jenkins-Host-IP>:8090
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Please check the logs above for details."
        }
    }
}
