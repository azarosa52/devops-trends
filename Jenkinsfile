pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        // Tomcat private IP (deploys)
        TOMCAT_URL        = 'http://172.31.1.10:8080'
        // Tomcat Elastic IP (browsing)
        TOMCAT_PUBLIC_URL = 'http://15.236.143.121:8080'
        // Tomcat application path
        CONTEXT_PATH      = '/devops-trends'
        // Folder that holds pom.xml ('.' = top level of the repository)
        APP_DIR           = '.'
        // WAR artifact name (derived from pom.xml artifactId)
        WAR_FILE          = 'devops-trends.war'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Pulling source code from GitHub...'
                git branch: 'main',
                    credentialsId: 'github-pat',
                    url: 'https://github.com/azarosa52/devops-trends.git'
            }
        }

        stage('Build') {
            steps {
                echo 'Compiling the application...'
                dir("${APP_DIR}") {
                    sh 'mvn -B clean compile'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                dir("${APP_DIR}") {
                    sh 'mvn -B test'
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging into WAR file...'
                dir("${APP_DIR}") {
                    sh 'mvn -B package -DskipTests'
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo "Deploying to Tomcat 10 at ${TOMCAT_URL}..."
                withCredentials([usernamePassword(
                    credentialsId: 'TomcatCreds',
                    usernameVariable: 'TC_USER',
                    passwordVariable: 'TC_PASS'
                )]) {
                    sh '''
                        curl -f -u "$TOMCAT_USER:$TOMCAT_PASS" \
                          -T target/devops-trends.war \
                          "http://172.31.1.10:8080/manager/text/deploy?path=/devops-trends&update=true"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            echo "Deployed to ${TOMCAT_URL}${CONTEXT_PATH} (Tomcat private IP)"
            echo "View the app at ${TOMCAT_PUBLIC_URL}${CONTEXT_PATH}"
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
