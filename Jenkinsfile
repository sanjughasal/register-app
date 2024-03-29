pipeline{
    agent{
        label 'Jenkins-Agent'
    }

     tools {
        jdk 'Java17'
        maven 'Maven3'
     }

 environment {
	    APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "sanjughasal"
        DOCKER_PASS = 'docker-token'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
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

                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token2'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
	    stage("Quality Gate"){
                  steps {
                            script {
                                      waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token2'
                                   }	
                      }

                                 }
	  stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }

       }
    }

}
