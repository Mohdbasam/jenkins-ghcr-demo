pipeline {

    agent any

    environment {
        IMAGE_NAME = "ghcr.io/mohdbasam/jenkins-ghcr-demo"
        IMAGE_TAG = "build-${BUILD_NUMBER}"
        GHCR_CREDENTIALS = credentials('ghcr-credentials')
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Cloning source code from GitHub...'

                git branch: 'main',
                    url: 'https://github.com/Mohdbasam/jenkins-ghcr-demo.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Running automated tests...'

                sh 'mvn test'
            }
        }

        stage('Build Application') {
            steps {
                echo 'Building Java application...'

                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'

                sh """
                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} \
                    .
                """
            }
        }

        stage('Tag Docker Image') {
            steps {
                echo 'Creating latest tag...'

                sh """
                    docker tag \
                    ${IMAGE_NAME}:${IMAGE_TAG} \
                    ${IMAGE_NAME}:latest
                """
            }
        }

        stage('Push Image to GHCR') {
            steps {
                echo 'Logging into GitHub Container Registry...'

                sh """
                    echo '${GHCR_CREDENTIALS_PSW}' | \
                    docker login ghcr.io \
                    -u '${GHCR_CREDENTIALS_USR}' \
                    --password-stdin
                """

                echo 'Pushing Docker image to GHCR...'

                sh """
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest
                """
            }
        }
    }

    post {

        success {
            echo 'Pipeline completed successfully!'

            mail(
                to: 'muhammedbasamkmail4u@gmail.com',
                subject: "SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Hello,

The Jenkins pipeline completed successfully.

Job: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}
Status: SUCCESS

Docker Image:
${IMAGE_NAME}:${IMAGE_TAG}

GHCR:
https://ghcr.io/mohdbasam/jenkins-ghcr-demo

Regards,
Jenkins
"""
            )
        }

        failure {
            echo 'Pipeline failed!'

            mail(
                to: 'muhammedbasamkmail4u@gmail.com',
                subject: "FAILED: ${JOB_NAME} #${BUILD_NUMBER}",
                body: """
Hello,

The Jenkins pipeline has FAILED.

Job: ${JOB_NAME}
Build Number: ${BUILD_NUMBER}
Status: FAILURE

Please check the Jenkins console output for the error.

Regards,
Jenkins
"""
            )
        }

        always {
            echo "Pipeline finished with status: ${currentBuild.currentResult}"
        }
    }
}
```
