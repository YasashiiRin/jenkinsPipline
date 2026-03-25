pipeline {
    agent {
        docker {
            image 'php-cli-custom'
            args '-v $HOME/.composer:/root/.composer'
        }
    }

    options {
        timeout(time:30, unit:'MINUTES')
        buildDiscarder(logRotator(numToKeepStr:'10')) //keep 10 builds
    }

    environment {
        APP_ENV = 'testing'
    }   
    stages {
        stage('Install') {
            steps {
                sh '''
                php -v
                composer -v
                composer install --no-interaction --prefer-dist --optimize-autoloader
                '''
            }
        }

        stage('Setup') {
            steps {
                sh '''
                cp .env.example .env
                php artisan key:generate
                php artisan config:cache
                php artisan route:cache
                php artisan view:cache
                php artisan event:cache
                php artisan cache:clear
                php artisan optimize
                php artisan migrate
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
