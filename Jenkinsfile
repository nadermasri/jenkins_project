pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'           // Virtual environment name
        DEPLOY_PATH = '/path/to/deployment' // Path to where the app will be deployed locally
    }
    stages {
        // Setup Stage: Creates a virtual environment and installs dependencies
        stage('Setup') {
            steps {
                script {
                    if (!fileExists("${env.WORKSPACE}/${VIRTUAL_ENV}")) {
                        sh "python3 -m venv ${VIRTUAL_ENV}"
                    }
                    sh "source ${VIRTUAL_ENV}/bin/activate && pip install -r requirements.txt"
                }
            }
        }

        // Lint Stage: Runs flake8 to check code style
        stage('Lint') {
            steps {
                script {
                    sh "source ${VIRTUAL_ENV}/bin/activate && flake8 app.py"
                }
            }
        }

        // Test Stage: Runs the unit tests with pytest
        stage('Test') {
            steps {
                script {
                    sh "source ${VIRTUAL_ENV}/bin/activate && pytest"
                }
            }
        }

        // Coverage Stage: Runs coverage analysis and generates a report
        stage('Coverage') {
            steps {
                script {
                    sh "source ${VIRTUAL_ENV}/bin/activate && coverage run -m pytest"
                    sh "source ${VIRTUAL_ENV}/bin/activate && coverage report"
                }
            }
        }

        // Security Scan Stage: Runs Bandit for security scanning
        stage('Security Scan') {
            steps {
                script {
                    sh "source ${VIRTUAL_ENV}/bin/activate && bandit -r app" // or `src` if applicable
                }
            }
        }

        // Deployment Stage: Deploy the application to a local directory
        stage('Deploy') {
            steps {
                script {
                    // If deploying locally (for local testing or to a local server)
                    echo "Deploying to local server..."
                    
                    // Step 1: Copy files to local deployment directory
                    sh "cp -r . ${DEPLOY_PATH}" // This will copy the entire workspace to the local server directory

                    // Step 2: Navigate to the deployment directory and install dependencies
                    sh """
                    cd ${DEPLOY_PATH}
                    source ${VIRTUAL_ENV}/bin/activate
                    pip install -r requirements.txt  // Install dependencies in the local server directory
                    """

                    // Step 3: Restart the application (this is just an example, adjust as needed)
                    // For example, if you're running the app via systemd or other service manager, you can restart it here.
                    // You can also directly start the application (e.g., Flask, Django, etc.) from this script.
                    echo "Starting the application..."
                    sh "source ${VIRTUAL_ENV}/bin/activate && python app.py"  // Adjust if using other entry point (e.g., Flask, Django, etc.)
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean up workspace after pipeline completion
        }
    }
}
