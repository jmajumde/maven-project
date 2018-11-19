pipeline {
    agent any
    tools {
        maven 'mymaven'
        jdk   'myjdk'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Packaging repo'
                sh 'mvn clean package'
            }
            post {
		success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                    sh '"docker images | grep tomcatwebapp"; [ $? -eq 0 ] && docker rmi -f tomcatwebapp:latest'
                    sh 'docker build . -t tomcatwebapp:latest'
                    sh 'docker run -d -p 8090:8090 tomcatwebapp:latest'
                }

            }
        }
        stage('Deploy to staging') {
            steps {
                echo 'Deploying package to stage'
                build job: 'deploy-to-staging'
            }
        }

        stage ('Deploy to Production'){
            steps{
                timeout(time:5, unit:'DAYS'){
                    input message:'Approve PRODUCTION Deployment?'
                }
                sh 'docker run -d -p 8091:8090 tomcatwebapp:latest'
                build job: 'Deploy-to-Prod'
            }
            post {
                success {
                    echo 'Code deployed to Production.'
                }

                failure {
                    echo ' Deployment failed.'
                }
            }
        }
    }
}
