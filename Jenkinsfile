pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '192.168.30.11', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '192.168.30.11', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }
    
    tools {
        maven 'mavenjenkins'
    }

stages{
        stage('Build'){
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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "scp **/target/*.war root@${params.tomcat_dev}:/tomcat-staging/webapps/"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp **/target/*.war root@${params.tomcat_prod}:/tomcat-prod/webapps/"
                    }
                }
            }
      post {
        success { 
            slackSend message: "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
        failure {
            slackSend message: "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }
        always {
            slackSend message: "Build Started - ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"
        }    
    }      
        }
    }
}
