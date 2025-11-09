// Use a Declarative Pipeline syntax
pipeline {
    agent any

    parameters {
        // Allows setting the specific version number for the built artifact
        string(name: 'APP_VERSION', defaultValue: '1.0.0', description: 'The version to stamp on the Python package.')
    }

    stages {
        stage('Checkout') {
            steps {
                // Ensure we start with a clean copy of the code
                checkout scm
            }
        }

        stage('Setup Environment & Test') {
            steps {
                sh 'echo "Building version ${params.APP_VERSION}"'
                
                // 1. Install dependencies into a virtual environment (best practice)
                sh 'python -m venv venv'
                sh '. venv/bin/activate && pip install --upgrade pip setuptools wheel'
                sh '. venv/bin/activate && pip install -r requirements.txt'
                
                // 2. Run your tests (e.g., using pytest)
                sh '. venv/bin/activate && pytest' 
            }
        }

        stage('Build Package') {
            steps {
                // 3. Build the distributable package (e.g., a wheel .whl file)
                // The version should be configured in your setup.py to use the ${params.APP_VERSION}
                sh '. venv/bin/activate && python setup.py sdist bdist_wheel'
                
                // Find the uniquely named wheel file created by the build process
                script {
                    def wheelFile = sh(returnStdout: true, script: "ls dist/*.whl").trim()
                    // Store the actual filename for archiving later
                    env.ARTIFACT_FILE = wheelFile
                    echo "Generated artifact: ${env.ARTIFACT_FILE}"
                }
            }
        }

        stage('Archive Artifact') {
            steps {
                // 4. Archive the unique artifact using the name determined in the previous stage
                archiveArtifacts artifacts: 'dist/*.whl', fingerprint: true
                sh 'echo "Artifact archived: ${env.ARTIFACT_FILE}"'
            }
        }
    }
}
