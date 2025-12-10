pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Bienvenue dans mon pipeline Jenkins depuis Jenkinsfile !'
            }
        }

        stage('Date') {
            steps {
                sh 'date'
            }
        }

        stage('Maven Version') {
            steps {
                sh 'mvn -version'
            }
        }
    }
}
