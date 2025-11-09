// Jenkinsfile (Declarative Pipeline)

pipeline {
    agent any
    stages {
        
        // --- Build ---
        stage('Build') {
            steps {
                echo "Building application on branch: ${env.BRANCH_NAME}"
                // Replace with your actual build command (e.g., mvn clean install, npm run build)
                sh 'echo "Running build process..."'
            }
        }

        // --- Test ---
        stage('Test') {
            steps {
                echo 'Running unit tests...'
                // Replace with your actual test command (e.g., mvn test, npm test)
                sh 'echo "Running tests..."'
            }
            // Optional: You can configure JUnit or other report collection here
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        // --- Deploy to Staging/Development (Runs on feature/develop branches) ---
        stage('Deploy to DEV/Staging') {
            // Conditional Execution: This stage runs ONLY if the branch is NOT 'main'
            when {
                not {
                    branch 'main' 
                }
            }
            steps {
                echo "Deploying branch ${env.BRANCH_NAME} to the Staging environment."
                // Replace with your staging deployment script/command
                sh './scripts/deploy_staging.sh'
            }
        }

        // --- Deploy to Production (Runs ONLY on the main branch) ---
        stage('Deploy to Production') {
            // Conditional Execution: This stage runs ONLY if the branch name is exactly 'main'
            when {
                branch 'main'
            }
            steps {
                echo '🚀 Deploying the main branch to Production!'
                // Replace with your production deployment script/command
                sh './scripts/deploy_production.sh'
            }
            // Optional: Add a manual approval gate before production deployment
            options {
                input(message: 'Proceed with Production Deployment?', ok: 'Deploy')
            }
        }
    }
    
    // Actions that run after all stages, regardless of success or failure.
    post {
        always {
            cleanWs() // Always clean the workspace to free up disk space
        }
        success {
            echo 'Pipeline finished successfully!'
            // Add notification steps here (e.g., Slack, Email)
        }
        failure {
            echo "Pipeline failed on branch: ${env.BRANCH_NAME}!"
        }
    }
}