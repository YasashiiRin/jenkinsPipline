pipeline {
    agent {
        docker {
            image 'php-cli-custom'
        }
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://your-repo-url.git'
            }
        }

        stage('Setup') {
            steps {
                sh '''
                php -v
                composer -v
                '''
            }
        }

        stage('Install') {
            steps {
                sh '''
                apt-get update
                apt-get install -y git unzip
                composer install
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'php artisan test'
            }
        }
    }

    post {
        success {
            echo "success....................."
        }
        failure {
            echo "failure............................"
        }
    }
}
