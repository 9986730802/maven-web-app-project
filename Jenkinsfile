pipeline {
    agent any
    tools {
        maven "maven-3.9.6"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/9986730802/maven-web-app-project.git'
            }
        }

        stage('Maven Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Sonar Report') {
            steps {
                sh "mvn sonar:sonar"
            }
        }

        stage('Artifact Deploy') {
            steps {
                sh "mvn clean deploy"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def tomcatUrl = 'http://13.201.54.111:8080/manager/text/deploy?path=/myapp&update=true'
                    def warFile = 'target/maven-web-application.war'

                    withCredentials([usernamePassword(credentialsId: 'tomcat-creds', usernameVariable: 'TOMCAT_USER', passwordVariable: 'TOMCAT_PASS')]) {
                        sh """
                        curl -T ${warFile} --user $TOMCAT_USER:$TOMCAT_PASS "${tomcatUrl}"
                        """
                    }
                }
            }
        }

    } // END stages

    post {
        success {
            emailext(
                to: 'manojreddyveluru@gmail.com',
                subject: "SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build succeeded!\n\nURL: ${env.BUILD_URL}"
            )
        }

        failure {
            emailext(
                to: 'manojreddyveluru@gmail.com',
                subject: "FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build failed!\n\nCheck logs: ${env.BUILD_URL}"
            )
        }
    }
} // END pipeline
