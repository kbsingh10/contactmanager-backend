pipeline {
    agent any

    environment {
        // Define your Docker registry and image name
        DOCKER_REGISTRY = 'prengineering'
        IMAGE_NAME      = 'backend'
        IMAGE_TAG       = "${env.BUILD_NUMBER}" // Uses Jenkins build number as tag
        
        // Python specific environment variables
        PIP_CACHE_DIR   = "${WORKSPACE}/.pip-cache"
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Setup Environment') {
            steps {
                echo 'Setting up Python Virtual Environment...'
                // Creates a local venv and installs/caches dependencies
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Lint & Code Quality') {
            steps {
                echo 'Running Flake8 Linting...'
                sh '''
                    sleep 10
                    #. venv/bin/activate
                    #pip install flake8
                    #flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics
                    #flake8 . --count --exit-zero --max-complexity=10 --max-line-length=127 --statistics
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running Unit Tests...'
                sh '''
                    . venv/bin/activate
                    pip install pytest pytest-cov
                    pytest --cov=app tests/
                '''
            }
        }

        stage('Package & Push') {
            steps {
                echo 'Building Docker Image...'
                // standard practice for deploying Python apps in containerized environments
                // script {
                //     docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-registry-credentials-id') {
                //         def customImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                //         customImage.push()
                        
                //         // Also push a 'latest' tag if this is the main branch
                //         if (env.BRANCH_NAME == 'main' || env.BRANCH_NAME == 'master') {
                //             customImage.push('latest')
                //         }
                    }
                }
            }
        }

    post {
        success {
            echo "Backend Build #${env.BUILD_NUMBER} succeeded and image pushed."
            // If you want this backend job to automatically tell the Orchestrator to deploy:
            // build job: 'orchestrator-pipeline-job', parameters: [string(name: 'BACKEND_VERSION', value: env.BUILD_NUMBER)], wait: false
        }
        failure {
            echo "Backend Build #${env.BUILD_NUMBER} failed. Check logs."
        }
    }