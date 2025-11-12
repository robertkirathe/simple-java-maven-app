pipeline {
    agent {
        kubernetes {
            label 'maven-agent'
            defaultContainer 'maven'
            yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: maven
    image: maven:3.8.7-eclipse-temurin-17
    command:
    - cat
    tty: true
"""
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                echo '🔨 Building the project...'
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                echo '🧪 Running tests...'
                sh 'mvn test'
            }
            post {
                always {
                    echo '📦 Publishing test results...'
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                echo '🚀 Delivering the build...'
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
