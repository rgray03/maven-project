pipeline {
    agent any

    parameters {
         string(name: 'tomcat_dev', defaultValue: '52.15.145.11', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '18.216.9.254', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                bat 'mvn clean package'
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
                        bat "winscp scp:ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps /privatekey=/Users/raugray/Downloads/tomcat-demo.ppk /upload **/target/*.war"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        bat "pscp -i /Users/raugray/Downloads/tomcat-demo.ppk /Program Files (x86)/Jenkins/workspace/FullyAutomated/webapp/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}