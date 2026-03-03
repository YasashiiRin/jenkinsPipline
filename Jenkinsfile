pipeline {
    agent any
    environment {
        APP_ENV = "piJenkin"
      }

    stages {
        stage('hello') {
            steps {
                echo "welcome to jenkins...."
                sh 'whoami'
                sh 'pwd'
              }
          }

        stage ("make folder") {
            steps {
                sh 'mkdir tmp_foulder'
              }
          }
      }
  }
