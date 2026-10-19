
pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        TOMCAT_URL = 'http://YOUR_TOMCAT_HOST:8080'
        APP_CONTEXT = '/devops-trends'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'mvn -B clean compile'
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
                echo 'Packaging WAR file...'
                sh 'mvn -B package -DskipTests'

                archiveArtifacts(
                    artifacts: 'target/devops-trends.war',
                    fingerprint: true
                )
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying application to Tomcat...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'tomcat-manager-creds',
                        usernameVariable: 'TOMCAT_USER',
                        passwordVariable: 'TOMCAT_PASS'
                    )
                ]) {
                    sh '''
                        set -eu
                        set +x

                        WAR_FILE="target/devops-trends.war"

                        if [ ! -f "$WAR_FILE" ]; then
                            echo "ERROR: WAR file not found: $WAR_FILE"
                            exit 1
                        fi

                        RESPONSE=$(mktemp)
                        trap 'rm -f "$RESPONSE"' EXIT

                        HTTP_CODE=$(curl --silent --show-error \
                            --connect-timeout 10 \
                            --max-time 300 \
                            --output "$RESPONSE" \
                            --write-out '%{http_code}' \
                            --user "$TOMCAT_USER:$TOMCAT_PASS" \
                            --upload-file "$WAR_FILE" \
                            "$TOMCAT_URL/manager/text/deploy?path=$APP_CONTEXT&update=true")

                        cat "$RESPONSE"

                        if [ "$HTTP_CODE" -lt 200 ] ||
                           [ "$HTTP_CODE" -ge 300 ] ||
                           ! grep -q '^OK -' "$RESPONSE"; then
                            echo "ERROR: Tomcat deployment failed (HTTP $HTTP_CODE)"
                            exit 1
                        fi

                        echo "Deployment successful."
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Review the stage error above.'
        }

        always {
            echo 'Pipeline finished.'
        }
    }
}
