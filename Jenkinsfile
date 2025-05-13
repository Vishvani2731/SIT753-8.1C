pipeline {
    agent any

    environment {
        MAVEN_HOME = '/usr/share/maven'
        PATH = "${env.MAVEN_HOME}/bin:${env.PATH}"
    }

    stages {

        stage('Build') {
            steps {
                echo 'Building the code...'
                sh 'mvn clean package'
            }
        }

        stage('Unit and Integration Tests') {
            steps {
                echo 'Running unit and integration tests...'
                sh 'mvn test'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running code analysis with SonarQube...'
                withSonarQubeEnv('MySonarQubeServer') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running security scan with OWASP Dependency-Check...'
                sh './dependency-check/bin/dependency-check.sh --project MyApp --scan . --format HTML'
            }
        }

        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging server...'
                sh 'scp target/*.jar ec2-user@<STAGING_EC2_IP>:/home/ec2-user/app.jar'
                sh 'ssh ec2-user@<STAGING_EC2_IP> "java -jar /home/ec2-user/app.jar &"'
            }
        }

        stage('Integration Tests on Staging') {
            steps {
                echo 'Running integration tests on staging...'
                sh 'newman run postman_collection.json'
            }
        }

        stage('Deploy to Production') {
            steps {
                input message: "Approve deployment to production?"
                echo 'Deploying to production server...'
                sh 'scp target/*.jar ec2-user@<PROD_EC2_IP>:/home/ec2-user/app.jar'
                sh 'ssh ec2-user@<PROD_EC2_IP> "java -jar /home/ec2-user/app.jar &"'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
