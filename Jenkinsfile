pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    docker run --rm \
                      --user "$(id -u):$(id -g)" \
                      -e HOME=/tmp \
                      -e npm_config_cache=/tmp/.npm \
                      -v "$WORKSPACE:/app" \
                      -w /app \
                      node:22-alpine \
                      npm ci
                '''
            }
        }

        stage('Project Checks') {
            steps {
                sh '''
                    docker run --rm \
                      --user "$(id -u):$(id -g)" \
                      -e HOME=/tmp \
                      -e npm_config_cache=/tmp/.npm \
                      -v "$WORKSPACE:/app" \
                      -w /app \
                      node:22-alpine \
                      npm run check
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    docker run --rm \
                      --user "$(id -u):$(id -g)" \
                      -e HOME=/tmp \
                      -e npm_config_cache=/tmp/.npm \
                      -v "$WORKSPACE:/app" \
                      -w /app \
                      node:22-alpine \
                      npm test
                '''
            }
        }
    }

    post {
        success {
            echo 'OpenCombat CI pipeline completed successfully!'
        }

        failure {
            echo 'OpenCombat CI pipeline failed. Check the console output.'
        }
    }
}
