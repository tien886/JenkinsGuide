pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'tien886/jenkins-guide-app'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
        REGISTRY_CREDENTIALS_ID = '150706'
    }

    stages {

        stage('Check Environment') {
            steps {
                sh 'python3 --version'
                sh 'pip3 --version'
                sh 'docker --version'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh 'pip3 install -r requirements.txt'
            }
        }

        stage('Auto Format') {
            steps {
                echo 'Auto formatting code with black...'
                sh 'python3 -m black app.py test_app.py'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'python3 test_app.py'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
            }
        }

        stage('Push Docker Image') {
            steps {
                echo 'Pushing Docker image...'

                withCredentials([
                    usernamePassword(
                        credentialsId: REGISTRY_CREDENTIALS_ID,
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''

                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }

        success {
            echo 'Pipeline succeeded!'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}