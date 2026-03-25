pipeline {
    agent {
        docker {
            image 'php-cli-custom'
          }
      }
    environment {

      }

    stages {
        stage('Checkout') {
            steps {
              git 'https://github.com/YasashiiRin/jenkinsPipline.git'
            }
        }

        stage ('Install') {
             steps {
                sh 'apt-get update'
                sh 'composer install'
              }
          }
        stage ("Build") {
            steps {


            }
          }

        stage ("Static Analysis") {


          }

        stage ("Test") {
            sh 'php artisan test'
          }

        stage ("Package") {


          }

        stage ("Deploy") {

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
