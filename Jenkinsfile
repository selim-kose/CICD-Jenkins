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
    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-credentials') // This refers to the Jenkins credentials ID
        AWS_SECRET_ACCESS_KEY = credentials('aws-credentials') // This will fetch the password as secret
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

                sh '''
                    # Example of using AWS CLI to list S3 buckets
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                    aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY

                    # Now you can run any AWS CLI commands or SDK calls
                    aws s3 ls
                '''

                //sh 'curl http://httpbin.org/get'
                //sh 'aws --version'
                //sh 'ls target'
                //sh 'aws s3 cp target/jenkins-pipeline-0.0.1-SNAPSHOT.jar s3://selim-jenkins'

            }
        }
    }
}