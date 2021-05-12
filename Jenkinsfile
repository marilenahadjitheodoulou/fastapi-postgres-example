pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
              // Get some code from a GitHub repository
                git branch: 'orm', url: 'https://github.com/tsadimas/fastapi-postgres-example.git'  
            }
        }
        stage('Create tag') {
            steps {
                script {
                    dockerTag = sh returnStdout: true, script: '''
                            #!/bin/bash -x
                            HEAD_COMMIT=$(git rev-parse --short HEAD)
                            LATEST_TAG=$(git rev-list --tags --max-count=1)
                            GIT_DESCRIBE=$(git describe --tags $LATEST_TAG)
                            TAG=${GIT_DESCRIBE:-v1.0.0}-$HEAD_COMMIT-$BUILD_ID
                            echo $TAG 
                        '''

                }
            }
        }
        stage('Test') {
            steps {
                sh '''
                    python3 -m venv fvenv
                    source fvenv/bin/activate
                    pip install -r requirements.txt
                    cd app
                    cp .env.example .env
                    rm test.db || true
                    pytest
                   '''
            }
        }

        stage('docker') {
            steps {
                sh '''
                    docker build --rm --no-cache -t ghcr.io/tsadimas/myfastapi:$DOCKER_VERSION -t ghcr.io/tsadimas/myfastapi:latest -t -f Dockerfile .  
                '''
            }
        }
    }
}