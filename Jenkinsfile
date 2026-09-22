pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        SONARQUBE_ENV = 'MySonarQubeServer'
        SLACK_CHANNEL = '#jenkins-alerts'
    }

    stages {

        stage('Git Clone') {
            steps {
                echo 'Cloning source code from GitHub...'
                git branch: 'main',
                    url: 'https://github.com/betawins/VProfile-1.git'
                sh 'ls -la'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube static code analysis...'
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Slack Notification') {
            steps {
                echo 'Sending build result to Slack...'
                slackSend(
                    channel: "${SLACK_CHANNEL}",
                    color: 'good',
                    message: "✅ Build SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}"
                )
            }
        }
    }

    post {
        failure {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'danger',
                message: "❌ Build FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}\n${env.BUILD_URL}"
            )
        }
    }
}
