pipeline {
    agent any

    tools {
        maven 'Maven 3.9.12'
    }

    environment {
        TOMCAT_URL        = 'http://172.31.6.237:8080'
        TOMCAT_PUBLIC_URL = 'http://15.236.143.121:8080'
        CONTEXT_PATH      = '/devops-trends'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling source code from GitHub...'
                git branch: 'main',
                    credentialsId: 'github-pat-jenkins',
                    url: 'https://github.com/azarosa52/devops-trends.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling the application...'
                sh 'mvn -version'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'mvn -B test'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging into WAR file...'
                sh 'mvn -B package -DskipTests'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying to Tomcat EC2 at ${TOMCAT_URL}..."

                withCredentials([usernamePassword(
                    credentialsId: 'jenkins-deployer',
                    usernameVariable: 'TOMCAT_USER',
                    passwordVariable: 'TOMCAT_PASS'
                )]) {
                    sh '''
                        curl -u "$TOMCAT_USER:$TOMCAT_PASS" \
                          -T target/devops-trends.war \
                          "http://172.31.6.237:8080/manager/text/deploy?path=/devops-trends&update=true"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            echo "Deployed to ${TOMCAT_URL}${CONTEXT_PATH} (Tomcat Private IP)"
            echo "View the app at ${TOMCAT_PUBLIC_URL}${CONTEXT_PATH}"
        }

        failure {
            echo 'Pipeline failed. Check console output for details.'
        }

        always {
            echo 'Pipeline finished. Cleaning workspace...'
            cleanWs()
        }
    }
}
