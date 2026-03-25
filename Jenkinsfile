pipeline {
    agent {
        docker {
            image 'php-cli-custom'
            args '-v $HOME/.composer:/root/.composer'
        }
    }

    options {
        timeout(time:30, unit:'MINUTES')
        buildDiscarder(logRotator(numtoKeepStr:10)) //keep 10 builds
    }

    environment {
        APP_ENV = 'testing'
    }   
    stages {
        stage('Setup') {
            steps {
                sh '''
                php -v
                composer -v
                cp .env.example .env
                php artisan key:generate
                php artisan config:cache
                php artisan route:cache
                php artisan view:cache
                php artisan optimize
                php artisan migrate
                '''
            }
        }

        stage('Install') {
            steps {
                sh '''
                composer install --no-interaction --prefer-dist --optimize-autoloader
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'php artisan test --no-interaction'
            }
        }
    }

    post {
        success {
            echo "success on branch ${env.BRANCH_NAME}"
        }
        failure {
            echo "failure on branch ${env.BRANCH_NAME}"
        }
    }
}
