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
                script {
                    if (!fileExists('.env')) {
                        sh 'cp .env.example .env'
                    }

                    if (env.APP_KEY == null || env.APP_KEY == '') {
                        sh 'php artisan key:generate'
                    }
                }
            }
        }

        stage('Setup') {
            steps {
                sh '''
                cp .env.example .env
                php artisan key:generate

                touch database/database.sqlite
                sed -i "s/DBDB_CONNECTION=mysql/DB_CONNECTION=sqlite/g" .env

                php artisan migrate

                php artisan config:cache
                php artisan route:cache
                php artisan event:cache
                php artisan cache:clear
                php artisan optimize
                
                '''
            }
        }

        stage ('Check lineing parallel') {
            parallel {
                stage ('lint PHP codeSniffer') {
                    steps {
                        script {
                            if (env.BRANCH_NAME == 'main' || !env.BRANCH_NAME) {
                                sh 'php vendor/bin/phpcs'
                            } else {
                                sh 'git fetch origin main'
                                def changeFile = sh(
                                    script: "git --no-pager diff origin/main --name-status 'app/*' 'config/*' 'routes/*' ':(exclude)*.blade.php' | grep -E '^(A|M|R|C)' | awk '{if (\$3 != \"\") print \$3; else print \$2}'",
                                returnStdout: true
                                ).trim().replace('\n', ' ')

                                if (changeFile) {
                                    sh "php vendor/bin/phpcs ${changeFile}"
                                } else {
                                    echo "No change files to lint"
                                }
                            }
                        }
                    }
                }
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
            echo "success on branch ${env.BRANCH_NAME}"
        }
        failure {
            echo "failure on branch ${env.BRANCH_NAME}"
        }
    }
}
