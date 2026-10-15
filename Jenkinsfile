
pipeline {
    agent any

    tools {
        maven 'maven 3.9.15'
        jdk 'JDK21'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling source code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling the application...'
                bat 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                bat 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application into a WAR file...'
                bat 'mvn package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo 'WAR package created. Configure Tomcat deployment here.'
                bat 'dir target'
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check console output for details.'
        }

        always {
            echo 'Pipeline finished.'
        }
    }
}
