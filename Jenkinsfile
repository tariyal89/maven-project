pipeline {
    agent any
       tools {
        maven 'LocalMaven'
       }

    parameters {
         string(name: 'tomcat_dev', defaultValue: '35.154.28.37', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '13.127.60.166', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
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
                        sh "scp -i /var/lib/jenkins/pramod.pem **/target/*.war ec2-user@${params.tomcat_dev}:/home/ec2-user/apache-tomcat-7.0.85/webapps/"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /var/lib/jenkins/pramod.pem **/target/*.war ec2-user@${params.tomcat_prod}:/home/ec2-user/apache-tomcat-7.0.85/webapps/"
                    }
                }
            }
        }
    }
}
