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
                withCredentials([usernamePassword(
                    credentialsId: 'tomcat-credentials',
                    usernameVariable: 'TOMCAT_USER',
                    passwordVariable: 'TOMCAT_PASS'
                )]) {
                    script {
                        def response = sh(
                            script: '''
                                curl -s -u "$TOMCAT_USER:$TOMCAT_PASS" \\
                                -T target/*.war \\
                                "http://15.236.143.121:8081/manager/text/deploy?path=/devops-trends&update=true"
                            ''',
                            returnStdout: true
                        ).trim()

                        echo "Tomcat response: ${response}"

                        if (response.contains("FAIL")) {
                            error("Deployment FAILED: ${response}")
                        } else {
                            echo "Deployment successful: ${response}"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            echo 'App running at: http://15.236.143.121:8081/devops-trends'
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