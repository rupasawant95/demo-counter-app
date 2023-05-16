pipeline{

    agent any
    environment {

        VERSION = "${env.BUILD_ID}"
    }

    stages{

        stage('sonar quality check'){

            agent{

                docker {
                    image 'maven'
                    args '-u root'
                }
            }
            steps{

                script{

                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'mvn clean package sonar:sonar'
                 }                  
                }
            }
        }
        stage('Quality Gate status'){

            steps{
                script{

                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }

        stage('docker build & docker push to Nexus repo'){

            steps{

                script{
                    withCredentials([string(credentialsId: 'nexus_passwd', variable: 'nexus_creds')]) {
                    sh '''
                     docker build -t 54.89.148.52:8083/springapp:${VERSION} .

                     docker login -u admin -p $nexus_creds 54.89.148.52:8083

                     docker push 54.89.148.52:8083/springapp:${VERSION}

                     docker rmi 54.89.148.52:8083/springapp:${VERSION}
                    '''
                    }
                }
            }
        }
    }
}
