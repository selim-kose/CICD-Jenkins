pipeline {
    agent {
        node {
            label 'docker-agent'
        }
    }
    triggers{
        pollSCM '* * * * *'
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout code from source control (e.g., Git)
                checkout scm
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('BUILD') {
            steps {
                echo 'Building...'
                sh 'chmod +x mvnw'
                sh './mvnw clean install'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}