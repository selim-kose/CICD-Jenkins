pipeline {
    agent {
        node {
            // Use the 'docker-agent' label to run this pipeline on a Docker agent
            label 'selim'
        }
    }
    triggers{
        //Poll SCM every minute
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
                echo 'Testing...'
            }
        }
        stage('BUILD') {
            steps {
                echo 'Building....'
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
                echo 'Deploying...'
            }
        }
        stage('AWS') {
            steps {
                sh 'curl http://httpbin.org/get'
                sh 'aws --version'
                sh 'aws s3 cp jenkins-pipeline-0.0.1-SNAPSHOT.jar s3://selim-jenkins'

            }
        }
    }
}