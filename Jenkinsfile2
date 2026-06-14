pipeline {
    agent any

    parameters {
        string(name: 'tomcat_dev', defaultValue: '192.168.33.11', description: 'Staging Server')
        string(name: 'tomcat_prod', defaultValue: '192.168.33.11', description: 'Production Server')
    }

    triggers {
        pollSCM('* * * * *')
    }

    tools {
        maven 'jenkins-maven'
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Prepare SSH') {
            steps {
                sh """
                mkdir -p ~/.ssh
                ssh-keyscan -H ${params.tomcat_dev} >> ~/.ssh/known_hosts
                ssh-keyscan -H ${params.tomcat_prod} >> ~/.ssh/known_hosts
                chmod 700 ~/.ssh
                chmod 600 ~/.ssh/known_hosts
                """
            }
        }

        stage('Deployments') {
            parallel {
                stage('Deploy to Staging') {
                    steps {
                        sh "scp **/target/*.war root@${params.tomcat_dev}:/tomcat-staging/webapps/"
                    }
                }

                stage('Deploy to Production') {
                    steps {
                        sh "scp **/target/*.war root@${params.tomcat_prod}:/tomcat-prod/webapps/"
                    }
                }
            }
        }
    }
}
