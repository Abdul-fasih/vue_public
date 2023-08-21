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
