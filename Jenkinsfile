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
        // Define the environment variables for the pipeline
        // In this example, we are using Jenkins credentials to store AWS access keys
        // The AWS Credential plugin will give you both the access key and secret key variables and you dont need to refer to them separately
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
                // Run multiple shell commands to interact with AWS CLI
                sh '''
                    # Example of using AWS CLI to list S3 buckets
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                    aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY

                    # Now you can run any AWS CLI commands or SDK calls
                    aws s3 ls
                    aws s3 cp target/jenkins-pipeline-0.0.1-SNAPSHOT.jar s3://selim-jenkins
                '''

            }
        }
    }
    post {
        always {
            echo 'Pipeline finished. Cleaning up...'
            cleanWs() // Clean workspace
        }
        success {
            echo 'Pipeline succeeded. Sending success notification...'
            emailext (
                subject: "Build Succeeded: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Good news! The build has succeeded. \n\nYou can find more details at ${env.BUILD_URL}.",
                to: 'selim.kose.s@example.com'
            )
        }
        failure {
            echo 'Pipeline failed. Sending failure notification...'
            emailext (
                subject: "Build Failed: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Unfortunately, the build has failed. \n\nCheck the logs at ${env.BUILD_URL}.",
                to: 'selim.kose.s@example.com'
            )
        }
    }
}