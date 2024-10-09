pipeline {
    agent {
        node {
            // Use the 'docker-agent' label to run this pipeline on a Docker agent
            label 'docker-agent'
        }
    }
    triggers{
        //Poll SCM every minute
        pollSCM '* * * * *'
    }
    stages {
        stage('Test installing AWS CLI') {
            steps {
                // Install AWS CLI
                sh 'curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"'
                sh 'chmod 777 awscli-bundle.zip'
                sh 'unzip awscli-bundle.zip'
                sh './awscli-bundle/install -b ~/bin/aws'
                sh 'aws --version'
            }
        }
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
                //add execute permission to mvnw
                sh 'chmod +x mvnw'
                //use maven wrapper to build the project, mvn is not available in the docker image
                sh './mvnw clean install'
            }
        }
        stage('List Target Directory') {
            steps {
                // List files in the target directory to verify the build output
                sh 'ls -al target'
                sh 'pwd'
            }
        }
        stage('Archive Artifacts') {
            steps {
                // Archive the built artifacts (JAR/WAR files)
                archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
                // Archive test results
                junit '**/target/surefire-reports/*.xml'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }

    }
}