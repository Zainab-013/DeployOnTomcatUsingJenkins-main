pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }

    environment {
        WAR_FILE = 'target/roshambo.war'  // forward slashes for Linux
        TOMCAT_URL = 'http://localhost:7080'
        TOMCAT_USER = 'war-deployer'
        TOMCAT_PASSWORD = 'maven-tomcat'
    }

    stages {
        stage('Clean Project') {
            steps {
                sh "mvn clean"
            }
        }

        stage('Build Project') {
            steps {
                sh "mvn package"
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFilePath = "${WORKSPACE}/${WAR_FILE}"
                    echo "WAR file path: ${warFilePath}"

                    if (fileExists(warFilePath)) {
                        echo 'WAR file found, deploying to Tomcat...'
                        sh """
                            curl --upload-file "${warFilePath}" \
                            --user ${TOMCAT_USER}:${TOMCAT_PASSWORD} \
                            "${TOMCAT_URL}/manager/text/deploy?path=/roshambo&update=true"
                        """
                    } else {
                        error('WAR file not found! Build failed.')
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Build completed'
        }
    }
}
