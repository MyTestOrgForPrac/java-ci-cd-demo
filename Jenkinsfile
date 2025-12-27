pipeline {
    agent {
        docker {
            image 'maven:3.9.2-openjdk-17-slim' // Linux Maven image with JDK
            label 'windows-docker-agent'        // Node must have Docker installed
            args '-v /var/run/docker.sock:/var/run/docker.sock' // Allows Docker-in-Docker
        }
    }

    environment {
        DOCKER_CREDS = credentials('docker-hub') // Jenkins credentials ID
        IMAGE_NAME = "java-ci-cd-demo:latest"    // Docker image name
        REGISTRY = "docker.io"                    // Optional registry prefix
    }

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Publish Coverage') {
            steps {
                publishHTML(target: [
                    reportDir: 'target/site/jacoco',
                    reportFiles: 'index.html',
                    reportName: 'JaCoCo Coverage',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: ${env.IMAGE_NAME}"
                sh "docker build -t ${env.IMAGE_NAME} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Logging in and pushing Docker image"
                sh """
                    echo "$DOCKER_CREDS_PSW" | docker login -u "$DOCKER_CREDS_USR" --password-stdin
                    docker push ${env.IMAGE_NAME}
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
        success {
            echo 'Build SUCCESS'
        }
        failure {
            echo 'Build FAILED'
        }
    }
}
