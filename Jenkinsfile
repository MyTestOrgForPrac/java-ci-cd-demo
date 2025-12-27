pipeline {
    agent any

    tools {
        maven 'maven-3.9'
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
                sh '''
                  mvn clean verify
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Publish Coverage') {
            steps {
                jacoco execPattern: 'target/jacoco.exec'
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
