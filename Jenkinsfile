pipeline {
    agent {
        docker {
            image 'maven:3.8.7-openjdk-21'
            args '-v /root/.m2:/root/.m2'
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
