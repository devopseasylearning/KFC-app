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
                            choice(
                                choices: ['DEV', 'QA', 'PREPROD'], 
                                name: 'ENVIRONMENT'
                            ),


                        string(
                             defaultValue: '50',
                             name: 'ui_tag',
                             description: '''Please enter ui image tage to be used''',
                            ),

                        string(
                             defaultValue: '50',
                             name:'KFC_tag',
                             description: '''Please ente7 KFC  image tage to be used''',
                            ),


                             string(name: 'WARNTIME',
                             defaultValue: '0',
                            description: '''Warning time (in minutes) before starting upgrade'''),

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
            notifyUpgrade(currentBuild.currentResult, "WARNING")
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
                       
                        docker push devopseasylearning/kfc:${BUILD_NUMBER} 
                        
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
                      sleep
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
ls
pwd
cat << EOF > KFC-app/kfc-charts/dev-values.yaml
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
        notifyUpgrade(currentBuild.currentResult, "POST")
      }
    }
    
  }



}






def notifyUpgrade(String buildResult, String whereAt) {
  if (Please_leave_this_section_as_it_is == 'origin/master') {
    channel = 'development-alerts'
  } else {
    channel = 'development-alerts'
  }
  if (buildResult == "SUCCESS") {
    switch(whereAt) {
      case 'WARNING':
        slackSend(channel: channel,
                color: "#439FE0",
                message: "s7-KFC-app: Upgrade starting in ${env.WARNTIME} minutes @ ${env.BUILD_URL}  Application s7-KFC-app")
        break
    case 'STARTING':
      slackSend(channel: channel,
                color: "good",
                message: "s7-KFC-app: Starting upgrade @ ${env.BUILD_URL} Application s7-KFC-app")
      break
    default:
        slackSend(channel: channel,
                color: "good",
                message: "s7-KFC-app: Upgrade completed successfully @ ${env.BUILD_URL}  Application s7-KFC-app")
        break
    }
  } else {
    slackSend(channel: channel,
              color: "danger",
              message: "s7-KFC-app: Upgrade was not successful. Please investigate it immediately.  @ ${env.BUILD_URL}  Application s7-KFC-app")
  }
}