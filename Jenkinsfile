pipeline {
    agent any // Runs on any available agent

    stages {
        stage('Build') {
            steps {
                echo "Building the project..."
                // Use 'sh' for Linux/macOS or 'bat' for Windows
                script {
                    if (isUnix()) {
                        sh 'ls -la'
                    } else {
                        bat 'dir'
                    }
                }
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                // Add your test commands here, e.g., sh 'npm test' or bat 'gradlew test'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying..."
                // Add your deployment commands here
            }
        }
    }
}
