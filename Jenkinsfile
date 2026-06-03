pipeline {
    agent any

    tools {
        maven 'maven 3.9.15'
        jdk 'JDK21'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Pulling sou
        }

        stage('Build') {
            ste
                bat 'mvn clean compile'
            }
    
            steps {
                echo 'Running tests...'
        
            steps {
                echo 'Packaging into WAR file...'
                bat 'mvn package -DskipTests'
            }
        }

            steps {
                echo 'Deploying to local Tomcat...'
                deploy adapters: [
                    tomcat9(
    
                    )
                ],
                cont
            echo 'App running at: http://localhost:8080/devops-trends'
        }
        failure {
            echo 'Pipeline failed. Check console output for details.'
        }
        always {
            echo 'Pipeline finished. Cleaning workspace...'
            deleteDir()
        }
    }
}