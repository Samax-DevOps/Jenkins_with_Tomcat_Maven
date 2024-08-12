pipeline{
    agent any
    tools{
        maven 'Maven'
    }

    stages{
        stage(Build){
            steps{
                sh 'mvn clean install'
            }
            post{
                success{
                    echo "archiving the artifacts"
                    archiveArtifacts artifact: '**/target/*.war'
                }
            }
        }
        stage(Deploy){
            steps{
                deploy adapters: [tomcat9(credentialsId: '282c0cd9-55cb-45ac-9221-83308b3f3021', path: '', url: 'http://52.91.214.7:8000/')], contextPath: null, onFailure: false, war: '**/*.war'

            }
        }
    }
}