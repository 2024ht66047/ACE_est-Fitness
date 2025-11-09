// This pipeline uses a declarative syntax suitable for Multibranch Pipeline jobs.
pipeline {
    // 1. Define the build environment using a Docker image.
    // This ensures your build environment is clean and consistent across agents.
    agent { 
        docker { 
            image 'python:3.10-slim'
            args '-u root' // Needed sometimes if Jenkins user lacks permissions inside the container
        }
    }

    // 2. Define environment variables. These are crucial for SonarQube identification.
    environment {
        // --- SONARQUBE CONFIGURATION ---
        // These variables MUST be customized to match your Jenkins and SonarQube setup.
        
        // This key will uniquely identify the project in SonarQube.
        // JOB_NAME is a built-in variable that works well for multi-branch environments.
        SONAR_PROJECT_KEY = "python-app-${JOB_NAME.replaceAll('%2F', '-')}"
        
        // This name must match the tool configuration in Manage Jenkins > Global Tool Configuration.
        SONAR_SCANNER_NAME = "ACE_estFitness_SonarQube" 
        
        // This name must match the server configuration in Manage Jenkins > Configure System.
        SONAR_SERVER_NAME = "SonarQube_Jenkins" 
    }

    // Define post-build actions, regardless of stage success/failure
    post {
        // Clean up the workspace to free up disk space
        always {
            cleanWs()
        }
    }

    stages {
        stage('Setup Environment') {
            steps {
                echo 'Installing Python dependencies...'
                // Install core dependencies
                sh 'pip install -r requirements.txt'
                // Install testing/coverage dependencies needed for analysis reports
                sh 'pip install pytest pytest-cov'
            }
        }

        stage('Run Unit Tests & Coverage') {
            steps {
                echo 'Running tests and generating coverage reports...'
                // Create a directory for reports
                sh 'mkdir -p reports'
                
                // Run tests:
                // --junitxml: Generates XML report for test results (used by SonarQube)
                // --cov=src: Measures coverage for the 'src' directory (change if needed)
                // --cov-report=xml: Generates XML coverage report (used by SonarQube)
                sh 'pytest --junitxml=reports/unit_results.xml --cov=src/ --cov-report=xml:reports/coverage.xml tests/'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Starting SonarQube analysis for branch: ${BRANCH_NAME}"
                
                // The withSonarQubeEnv step injects the necessary credentials and URL
                // defined in the Jenkins global configuration.
                withSonarQubeEnv(installationName: env.SONAR_SERVER_NAME) {
                    
                    // Execute the SonarScanner CLI
                    sh "${tool env.SONAR_SCANNER_NAME}/bin/sonar-scanner " +
                        "-Dsonar.projectKey=${env.SONAR_PROJECT_KEY} " +
                        "-Dsonar.projectName=${env.SONAR_PROJECT_KEY} " +
                        "-Dsonar.sources=src " +
                        "-Dsonar.tests=tests " +
                        // Specify the location of the coverage report generated in the previous stage
                        "-Dsonar.python.coverage.reportPaths=reports/coverage.xml " +
                        // Specify the location of the JUnit test results
                        "-Dsonar.junit.reportPaths=reports/unit_results.xml " +
                        // Pass the branch name (automatic in Multi-branch, but good practice)
                        "-Dsonar.branch.name=${env.BRANCH_NAME}"
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                echo 'Waiting for SonarQube Quality Gate status...'
                // Wait up to 5 minutes for SonarQube to finish processing and send the status back.
                // If the Quality Gate is not green, abortPipeline: true will fail the build.
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy (Example)') {
            when {
                // This stage only runs if the branch is 'main' AND the Quality Gate passed.
                branch 'main'
            }
            steps {
                echo "Code is clean and ready for production deployment from ${BRANCH_NAME}!"
                // sh './deploy.sh'
            }
        }
    }
}