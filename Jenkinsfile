pipeline{
    agent any
    tools{
        maven 'Maven'
    }

    stages{
        stage ('Build'){
            steps{
                sh 'mvn clean install'
            }
            post{
                success{
                    echo "archiving the artifacts"
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage ('Deploy'){
            steps{
                deploy adapters: [tomcat9(credentialsId: '6684a7c0-c154-4917-8a0b-54e978b723a8', path: '', url: 'http://10.0.2.15:8000/')], contextPath: null, war: '**/*.war'

            }
        }
    }
}
