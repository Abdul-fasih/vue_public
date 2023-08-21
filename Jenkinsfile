pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing.......'
            }
        }

        stage('Deploy to Nginx') {
            steps {
                script {
                    echo 'Copying files to Nginx...'
                    sh 'echo 87871524 | sudo -S cp -r dist/css dist/favicon.ico dist/index.html dist/js /var/www/html/vue/dist'
                }
                // Reload Nginx to apply changes
                sh 'sudo systemctl reload nginx'
            }
        }

        stage('Notify Slack') {
            steps {
                script {
                    def slackWebhook = 'https://hooks.slack.com/services/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'
                    def slackMessage

                    if (currentBuild.resultIsBetterOrEqualTo("SUCCESS")) {
                        slackMessage = """
                        {
                            'text': 'Deployment successful! - Vue App',
                            'attachments': [
                                {
                                    'color': '#36a64f',
                                    'title': 'Details',
                                    'fields': [
                                        {
                                            'title': 'Status',
                                            'value': 'Success',
                                            'short': true
                                        },
                                        {
                                            'title': 'Build Number',
                                            'value': '${BUILD_NUMBER}',
                                            'short': true
                                        }
                                    ]
                                }
                            ]
                        }
                        """
                    } else {
                        slackMessage = """
                        {
                            'text': 'Deployment failed! - Vue App',
                            'attachments': [
                                {
                                    'color': '#ff0000',
                                    'title': 'Details',
                                    'fields': [
                                        {
                                            'title': 'Status',
                                            'value': 'Failed',
                                            'short': true
                                        },
                                        {
                                            'title': 'Build Number',
                                            'value': '${BUILD_NUMBER}',
                                            'short': true
                                        }
                                    ]
                                }
                            ]
                        }
                        """
                    }

                    sh """
                    curl -X POST -H 'Content-type: application/json' --data '${slackMessage}' ${slackWebhook}
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
