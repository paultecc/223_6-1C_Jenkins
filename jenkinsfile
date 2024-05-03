pipeline {
    agent any 

    environment {
        DIRECTORY_PATH = '/Users/pauleccarius/Library/CloudStorage/OneDrive-DeakinUniversity/SIT223 Professional Practice in Information Technology/5.1P/' 
        TESTING_ENVIRONMENT = 'Test_Environment' 
        PRODUCTION_ENVIRONMENT = 'Paul_Eccarius'
    }

    tools {
        maven  'Maven'
    }

    stages {
        stage('0 Delay') {
            steps {
                // The job should be triggered with a short delay whenever a new commit is pushed to the GitHub repository
                sleep 5
            }
        }
        
        stage('1 Build') {
            steps {
                // Build the code using a build automation tool to compile and package your code. You need to specify at least one build automation tool e.g., Maven.
                echo "fetch the source code from the directory path specified by the environment variable"
                checkout scm //configure in Jenkins
                echo "compile the code and generate any necessary artifacts" 
                sh 'mvn clean compile'
            }
        }

        stage('2 Test') {
            steps {
                // run unit tests to ensure the code functions as expected and run integration tests to ensure the different components
                // of the application work together as expected. You need to specify test automation tools for this stage
                echo "unit tests"
                echo "integration tests"
                sh  'mvn test'
                junit '**/target/surefire-reports/*.xml' 

            }
            post {
                success {
                    emailext (
                        to: env.NOTIFICATION_EMAIL, // add email to Jenkins environment variables
                        subject: '223_6-1 Jenkins - ${STAGE_NAME} Stage SUCCESS',
                        body: '<p>${STAGE_NAME} stage completed successfully, logs attached.</p>',
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }
                failure {
                    emailext (
                        to: env.NOTIFICATION_EMAIL, // add email to Jenkins environment variables
                        subject: '223_6-1 Jenkins - ${STAGE_NAME} Stage FAILURE',
                        body: '<p>${STAGE_NAME} stage failed to complete, logs attached.</p>',
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }    
            }
        }

        stage('3 Code Quality Check') {
            steps {
                // integrate a code analysis tool to analyse the code and ensure it meets industry standards. Research and select a tool to analyse your code using  Jenkins.
                echo "check the quality of the code" // Make sure PMD is set up in Jenkins

                sh 'pmd -d src -R rulesets/java/basic.xml -f xml > pmd_report.xml'
                pmd canRunOnFailed: true, healthReportAmplificationFactor: 1.0, pattern: 'pmd_report.xml'
            }
        }

        stage('4 Security Scan') {
            steps {
                // perform a security scan on the code using a tool to identify any vulnerabilities. Research and select a tool to scan your code.
                echo "security scan"

                sh 'dependency-check.sh --project "hello-world" --scan ./ --out ./ --format XML' 
                dependencyCheckPublisher canRunOnFailed: true, pattern: 'dependency-check-report.xml'  

            }
            post {
                success {
                    emailext (
                        to: env.NOTIFICATION_EMAIL, // add email to Jenkins environment variables
                        subject: '223_6-1 Jenkins - ${STAGE_NAME} Stage SUCCESS',
                        body: '<p>${STAGE_NAME} stage completed successfully, logs attached.</p>',
                        attachLog: true, 
                        mimeType: 'text/html'
                    )
                }
                failure {
                    emailext (
                        to: env.NOTIFICATION_EMAIL, // add email to Jenkins environment variables
                        subject: '223_6-1 Jenkins - ${STAGE_NAME} Stage FAILURE',
                        body: '<p>${STAGE_NAME} stage failed to complete, logs attached.</p>',
                        attachLog: true,
                        mimeType: 'text/html'
                    )
                }    
            }
        }

        stage('5 Deploy') {
            steps {
                // deploy the application to a staging server (e.g., AWS EC2 instance)
                echo "deploy the application to a testing environment specified by the environment variable"
                // EC2 instance details and credential would be entered here (for example)
            }
        }

        stage('6 Integration Test') {
            steps {
                // run integration tests on the staging environment to ensure the application functions as expected in a production-like environment.
                echo "run integration tests on the staging environment"
                sh 'mvn integration-test' 
            }
        }

        stage('7 Deploy to Production') {
            steps {
                // Deploy to Production - deploy the application to a production server (e.g., AWS EC2 instance).
                echo "deploy: ${env.PRODUCTION_ENVIRONMENT}"
            }
        }
    }
}
