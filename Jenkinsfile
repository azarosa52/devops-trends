pipeline {
    agent any

    tools {
        jdk 'JDK 21'
        maven 'Maven-Auto'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling source code from GitHub...'

                git branch: 'main',
                    credentialsId: 'GitHubCreds',
                    url: 'https://github.com/azarosa52/devops-trends.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling the application...'
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging into WAR file...'
                sh 'mvn package -DskipTests'

                archiveArtifacts artifacts: 'target/*.war',
                                 fingerprint: true
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying application to Tomcat...'

                deploy adapters: [
                    tomcat10(
                        credentialsId: 'Tomcat-jenkins-Creds',
                        path: '/manager/text',
                        url: 'http://15.236.143.121:8081'
                    )
                ],
                contextPath: '/devops-trends',
                war: 'target/*.war'
            }
        }
    }

    post {
        success {
            echo 'Build and deployment successful!'
            echo 'Application URL: http://15.236.143.121:8081/devops-trends'
        }

        failure {
            echo 'Pipeline failed. Check Console Output for details.'
        }

        always {
            echo 'Pipeline finished.'
            cleanWs()
        }
    }
}
