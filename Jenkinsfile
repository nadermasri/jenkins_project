pipeline {
    agent any
    environment {
        VIRTUAL_ENV = 'venv'
        DEPLOY_DIR = '/Users/naderalmasri/Desktop/AUB/Fall 2024-2025/EECE435L/jenkins_project'
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
                    sh "source ${VIRTUAL_ENV}/bin/activate && bandit -r ${DEPLOY_DIR}"
                }
            }
        }

        // Deployment Stage: Deploys the app to the local directory
        stage('Deploy') {
            steps {
                script {
                    echo "Deploying application to ${DEPLOY_DIR}"

                    // Copy files to the local server deployment directory
                    sh """
                        cp -r * ${DEPLOY_DIR}/
                        cd ${DEPLOY_DIR}
                        source ${VIRTUAL_ENV}/bin/activate
                        pip install -r requirements.txt
                    """
                }
            }
        }
    }

    // Clean up workspace after pipeline completion
    post {
        always {
            cleanWs()
        }
    }
}
