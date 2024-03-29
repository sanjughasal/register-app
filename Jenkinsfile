pipeline{
    agent{
        label 'Jenkins-Agent'
    }

     tools {
        jdk 'Java17'
        maven 'Maven3'
     }





    stages{
        stage("Cleanup Workspace"){
            steps{
                cleanWs()
            }
        }

        stage("Checkout from SCM"){
            steps{
                git branch: 'main' , credentialsId: 'github' , url: 'https://github.com/sanjughasal/register-app'
            }
        }

        stage("Build Application"){
            steps{
                sh "mvn clean package"
            }
        }

        stage("test Application"){
            steps{
                sh "mvn test "
            }
        }

        stage("SonarQube Analysis"){
            steps{
                script{

                    withSonarQubeEnv(credentialsId: 'Jenkins-sonarqube-token'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
    }

}
