pipeline {
    agent {
        kubernetes {
            label 'maven-agent'
            defaultContainer 'maven'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/label: maven-agent
spec:
  containers:
    - name: maven
      image: maven:3.9.6-eclipse-temurin-17
      command:
        - cat
      tty: true
      resources:
        requests:
          memory: "256Mi"
          cpu: "250m"
        limits:
          memory: "512Mi"
          cpu: "500m"
"""
        }
    }

    stages {
        stage('Debug Info') {
            steps {
                echo "Running on pod: ${env.NODE_NAME}"
                sh 'echo "Current user: $(whoami)"'
                sh 'echo "Java version:" && java -version'
                sh 'echo "Maven version:" && mvn -version'
            }
        }

        stage('Checkout') {
            steps {
                echo "Cloning repository..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building the Maven project..."
                sh 'mvn -B clean package'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'mvn test'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }

    post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed. Check pod logs for details."
        }
    }
}
