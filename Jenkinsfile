pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
    SONARQUBE_ENV = 'sonarqube'
    SLACK_CHANNEL = '#jenkins-notifier'
}
    stages {

        stage('Build') {
            steps {
                echo 'Building VProfile application...'

                sh 'ls -la'

                sh 'mvn clean verify'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube static code analysis...'

                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }

    post {

        success {
            echo 'Build completed successfully.'

            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'good',
                message: "BUILD SUCCESS\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nURL: ${env.BUILD_URL}"
            )
        }

        failure {
            echo 'Build failed.'

            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'danger',
                message: "BUILD FAILED\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}\nURL: ${env.BUILD_URL}"
            )
        }

        always {
            echo "Build completed with status: ${currentBuild.currentResult}"
        }
    }
}

