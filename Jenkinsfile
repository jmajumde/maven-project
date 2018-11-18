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
                }

            }
        }
        stage('Deploy to staging') {
            steps {
                echo 'Deploying package to stage'
                build job: 'deploy-to-staging'
            }
        }
    }
}
