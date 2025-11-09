// Use a Declarative Pipeline syntax
pipeline {
    // We set 'agent none' at the top level because the agent is defined per stage
    agent none 

    parameters {
        // Allows setting the specific version number for the built artifact
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'The version to stamp on the Python package.')
    }

    // 🎯 FIX: Define the Linux-style working directory for the container once
    // This environment variable is used by the Docker Pipeline plugin to set the -w flag.
    environment {
        // Must be a valid POSIX (Linux) path
        DOCKER_CONTAINER_WORKING_DIR = '/usr/src/app' 
        // We use the full workspace path in the volume mount arguments later
        WINDOWS_WORKSPACE_PATH = 'C:/ProgramData/Jenkins/.jenkins/workspace/ACE_est_Basic_Jenkins_job'
    }

    stages {
        stage('Checkout') {
            // Run checkout on the Windows agent (the Jenkins master)
            agent any 
            steps {
                checkout scm
            }
        }
        
        stage('Build and Test') {
            // Use the Python container to execute the build steps
            agent {
                docker {
                    image 'python:3.11-slim' 
                    // 🚨 CRITICAL FIX for Windows Host 🚨
                    // Explicitly define the volume mount using the Windows path (source)
                    // and the Linux path (target) to ensure the workspace is accessible.
                    // This forces the Docker run command to use your specified paths.
                    args "-v ${env.WINDOWS_WORKSPACE_PATH}:${env.DOCKER_CONTAINER_WORKING_DIR}"
                    alwaysPull true
                }
            }
            
            steps {
                sh 'echo "Current directory inside container:"'
                sh 'pwd' // Should output: /usr/src/app
                sh 'echo "Building version ${params.APP_VERSION}"'
                
                // 1. Setup Environment & Install Dependencies (inside container)
                sh 'python -m venv venv'
                sh '. venv/bin/activate && pip install --upgrade pip setuptools wheel'
                sh '. venv/bin/activate && pip install -r requirements.txt'
                
                // 2. Run Tests
                sh '. venv/bin/activate && pytest' 
                
                // 3. Build the Distributable Package
                // (Assumes your setup.py correctly uses the version parameter)
                sh '. venv/bin/activate && python setup.py sdist bdist_wheel'
            }
        }

        stage('Archive Artifact') {
            // Switch back to 'any' agent to ensure the 'archiveArtifacts' 
            // step has direct access to the files on the Windows host.
            agent any 
            steps {
                // Archive the generated package files (e.g., .whl and .tar.gz)
                archiveArtifacts artifacts: 'dist/*.whl, dist/*.tar.gz', fingerprint: true
                echo 'Artifacts successfully archived on the Jenkins master.'
            }
        }
    }
}
