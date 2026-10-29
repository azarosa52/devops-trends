pipeline {
    agent any

    tools {
        maven 'Maven 3.9.15'
        jdk 'JDK21'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Pulling source code from GitHub...'
                git branch: 'main',
                    url: 'https://github.com/azarosa52/devops-trends.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling the application...'
                timeout(time: 10, unit: 'MINUTES') {
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                timeout(time: 10, unit: 'MINUTES') {
                    sh 'mvn test'
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging into WAR file...'
                timeout(time: 10, unit: 'MINUTES') {
                    sh 'mvn package -DskipTests'
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying to Tomcat...'
                script {
                    deploy adapters: [tomcat10(credentialsId: 'tomcat-credentials', url: 'http://15.236.143.121:8081')],
                           contextPath: 'devops-trends',
                           war: 'target/*.war'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            echo 'App running at: http://15.236.143.121:8080/devops-trends'
        }
        failure {
            echo 'Pipeline failed. Check console output for details.'
        }
        always {
            echo 'Pipeline finished.'
            cleanWs()
        }
    }
}
