pipeline {
    agent any

    tools {
        maven 'maven9.10'
        git 'Default'
    }

    stages {
        stage('GitCheckout') {
            steps {
                git branch: 'main', url: 'https://github.com/Poona25desai/maven-web-application.git'
            }
        }

        stage('MavenBuild') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarAnalysis') {
            steps {
                sh 'mvn sonar:sonar'
            }
        }

        stage('NexusUpload') {
            steps {
                sh 'mvn deploy'
            }
        }

        stage('TomcatHosting') {
            steps {
                sshagent(['Tomcat']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no target/maven-web-application.war ubuntu@13.201.123.73:/opt/apache-tomcat-9.0.106/webapps/
                    '''
                }
            }
        }
    }
    post {
            success {
                slackSend (
                    color: 'good', // green
                    message: "✅ Build Success - Job: ${env.JOB_NAME}, Build: #${env.BUILD_NUMBER}\n<${env.BUILD_URL}|Click here to view details>"
                )
            }
            failure {
                slackSend (
                    color: 'danger', // red
                    message: "❌ Build Failed - Job: ${env.JOB_NAME}, Build: #${env.BUILD_NUMBER}\n<${env.BUILD_URL}|Click here to view details>"
                )
            }
            unstable {
                slackSend (
                    color: 'warning', // yellow
                    message: "⚠️ Build Unstable - Job: ${env.JOB_NAME}, Build: #${env.BUILD_NUMBER}\n<${env.BUILD_URL}|Click here to view details>"
                )
            }
        }	  
}
