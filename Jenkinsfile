pipeline {
    agent any 
    options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
        timeout (time: 60, unit: 'MINUTES')
        timestamps()
      }
    environment {
        DOCKERHUB_CREDS = credentials('dockerhub-creds') 
    }


    stages {
        stage('Setup parameters') {
            steps {
                script {
                    properties([
                        parameters([


                             string(name: 'WARNTIME',
                             defaultValue: '0',
                            description: '''Warning time (in minutes) before starting KFC '''),

                        string(
                             defaultValue: 'develop',
                             name: 'Please_leave_this_section_as_it_is',
                            ),
                        ]),



                    ])
                }
            }
        }


       stage('warning') {
      steps {
        script {
            notifyKFC (currentBuild.currentResult, "WARNING")
            sleep(time:env.WARNTIME, unit:"MINUTES")
        }
      }
    }




        stage('Build binaries') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                     sleep 10
                    '''
                }
            }
        }


        stage('Build executable') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                     sleep 10
                    '''
                }
            }
        }



        stage('static test') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                     sleep 10
                    '''
                }
            }
        }


        stage('unit test') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                     sleep 10
                    '''
                }
            }
        }




         stage('SonarQube analysis') {

            agent {
                docker {
                  image 'sonarsource/sonar-scanner-cli:4.7.0'
                }
               }
               environment {
        CI = 'true'
        //  scannerHome = tool 'Sonar'
        scannerHome='/opt/sonar-scanner'
    }
            steps{
                withSonarQubeEnv('Sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }




        stage('Docker Login') {
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        echo "${DOCKERHUB_CREDS_PSW}" | docker login --username "${DOCKERHUB_CREDS_USR}" --password-stdin
                    '''
                }
            }
        }



        stage('Build client') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                       
                        docker build -t devopseasylearning/kfc:v1.0.${BUILD_NUMBER} .
                        
                    '''
                }
            }
        }



        stage('push client ') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                       
                        docker push devopseasylearning/kfc:v1.0.${BUILD_NUMBER}
                        
                    '''
                }
            }
        }



        stage('Build api') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                     sleep 10
                    '''
                }
            }
        }


        stage('push api ') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        
                        sleep 10
                      
                    '''
                }
            }
        }

        stage('Build database') {
 
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                     sleep 10
                    '''
                }
            }
        }



        stage('push database ') {
    
            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                        sleep 10
                      
                    '''
                }
            }
        }



        stage('End to End test ') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                      sleep 10
                    '''
                }
            }
        }

   stage('Update KFC-app  charts') {

            steps {
                script {

                    sh '''
rm -rf S4-projects-charts || true
git clone git@github.com:devopseasylearning/S4-projects-charts.git
cd S4-projects-charts

cat << EOF > kfc-charts/dev-values.yaml
image:
  repository: devopseasylearning/kfc
  tag: v1.0.${BUILD_NUMBER}
EOF


git config --global user.name "devopseasylearning"
git config --global user.email "info@devopseasylearning.com"

git add -A 
git commit -m "change from jenkins CI"
git push 
                    '''
                }
            }
        }





        stage('Launch/deploy application ') {

            steps {
                script {
                    // Log in to Docker Hub
                    sh '''
                      sleep 200
                    '''
                }
            }
        }

    }

post {
    always {
      script {
        notifyKFC (currentBuild.currentResult, "POST")
      }
    }
    
  }

}





def notifyKFC (String buildResult, String whereAt) {
  if (Please_leave_this_section_as_it_is == 'origin/develop') {
    channel = 'development-alerts'
  } else {
    channel = 'development-alerts'
  }
  if (buildResult == "SUCCESS") {
    switch(whereAt) {
      case 'WARNING':
        slackSend(channel: channel,
                color: "#439FE0",
                message: "KFC-app:   starting in ${env.WARNTIME} minutes @ ${env.BUILD_URL} ")
        break
    case 'STARTING':
      slackSend(channel: channel,
                color: "good",
                message: "KFC-app: Starting   @ ${env.BUILD_URL}")
      break
    default:
        slackSend(channel: channel,
                color: "good",
                message: "KFC-app:   completed successfully @ ${env.BUILD_URL} ")
        break
    }
  } else {
    slackSend(channel: channel,
              color: "danger",
              message: "KFC-app:   was not successful. Please investigate it immediately.  @ ${env.BUILD_URL} ")
  }
}
