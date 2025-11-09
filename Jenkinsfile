// Jenkinsfile (Declarative Pipeline)
// This pipeline executes a standard CI/CD flow with Python-specific build steps
// and archives the build artifacts upon success.

pipeline {
    agent any

    // Environment variables used throughout the pipeline
    environment {
        // Specify the Python version/interpreter if needed
        PYTHON_VERSION = 'python3' 
        ARTIFACT_DIR = 'dist'
    }

    stages {
        
        // --- Stage 1: Build & Package Python Application ---
        stage('Build') {
            steps {
                echo "Building application on branch: ${env.BRANCH_NAME}"
                
                // 1. Setup Virtual Environment (Recommended practice)
                sh "${env.PYTHON_VERSION} -m venv venv"
                sh 'source venv/bin/activate'
                
                // 2. Install dependencies (assuming requirements.txt exists)
                sh 'pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
                
                // 3. Build the distribution package (requires 'setuptools' and 'wheel')
                // This creates files in the 'dist' directory (e.g., .whl, .tar.gz)
                sh 'pip install setuptools wheel'
                sh "${env.PYTHON_VERSION} setup.py sdist bdist_wheel"
                
                echo "Python package successfully built into the ${env.ARTIFACT_DIR} directory."
            }
        }

        // --- Stage 2: Test ---
        stage('Test') {
            steps {
                echo 'Running unit tests...'
                // Run tests using the virtual environment
                sh 'source venv/bin/activate'
                sh 'pytest' // Assuming you use pytest, change this to your test runner
            }
            post {
                // Archive test reports if they are generated
                always {
                    junit '**/test-reports/*.xml'
                }
            }
        }

        // --- Stage 3: Deploy to DEV/Staging (Runs on feature/develop branches) ---
        stage('Deploy to DEV/Staging') {
            when {
                not {
                    branch 'main' 
                }
            }
            steps {
                echo "Deploying branch ${env.BRANCH_NAME} to the Staging environment."
                sh './scripts/deploy_staging.sh'
            }
        }

        // --- Stage 4: Deploy to Production (Runs ONLY on the main branch) ---
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                echo '🚀 Deploying the main branch to Production!'
                // In a real scenario, this would deploy the archived artifact
                sh './scripts/deploy_production.sh'
            }
            options {
                input(message: 'Proceed with Production Deployment?', ok: 'Deploy')
            }
        }
    }
    
    // 3. Post: Actions that run after all stages.
    post {
        always {
            // Clean the workspace, including the venv
            cleanWs() 
        }
        success {
            echo 'Pipeline finished successfully! Archiving artifacts...'
            // *** Archiving the Python built files from the 'dist' directory ***
            archiveArtifacts artifacts: "${env.ARTIFACT_DIR}/**", fingerprint: true
            
            // Add notification steps here (e.g., Slack, Email)
        }
        failure {
            echo "Pipeline failed on branch: ${env.BRANCH_NAME}!"
        }
    }
}
