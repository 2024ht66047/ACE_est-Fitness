// In your Jenkinsfile
pipeline {
    // Run the entire pipeline on the Windows agent
    agent any 

    parameters {
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'The version to stamp on the Python package.')
    }

    // Define variables if needed
    environment {
        // Windows path to the virtual environment activation script
        VENV_ACTIVATE = '.\\venv\\Scripts\\activate.bat' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Environment & Test') {
            steps {
                bat 'echo "Building version %APP_VERSION%"'
                
                // 1. Setup Virtual Environment (Windows commands)
                // Use python -m venv to create the environment
                bat 'python -m venv venv'
                
                // 2. Install Dependencies (needs to be run in the activated environment)
                // We use CALL to run the activation script and commands in the same shell
                bat """
                    CALL %VENV_ACTIVATE%
                    pip install --upgrade pip setuptools wheel
                    pip install -r requirements.txt
                    pytest
                """
            }
        }

        stage('Build Package') {
            steps {
                // Build the distributable package
                bat """
                    CALL %VENV_ACTIVATE%
                    python setup.py sdist bdist_wheel
                """
            }
        }

        stage('Archive Artifact') {
            steps {
                // Archive the generated package files (the files are on the Windows host)
                archiveArtifacts artifacts: 'dist/*.whl, dist/*.tar.gz', fingerprint: true
                echo 'Artifacts successfully archived on the Jenkins master.'
            }
        }
    }
}
