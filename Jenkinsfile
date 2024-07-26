@Library('shared-library') _

pipeline {
    agent {
        label 'agent'
    }

    environment {
      PIP_BREAK_SYSTEM_PACKAGES = "1"
      scannerHome = tool 'SonarQube'
    }

    stages {
      stage('Get Code') {
        steps {
          checkout scm
        }
      }
    

    stage('Run test') {
        steps {
          runTests()
        }
      }

        stage('Sonarqube analysis') {
            steps {
                runSonarAnalysis(scannerHome)
                }
            }
        }

        stage('Build application image') {
            steps {
                script {
                  // Prepare basic image for application
                  dockerTag = "${env.BUILD_ID}.${env.GIT_COMMIT.take(7)}"
                  applicationImage = docker.build("kszarpak/frontend:$dockerTag",".")
                }
            }
        }
        stage ('Pushing image to docker registry') {
            steps {
                script {
                    docker.withRegistry("", "dockerhub") {
                        applicationImage.push()
                        applicationImage.push('latest')
                    }
                }
            }
        }
    
    }

    post {
        success {
            build wait: false, job: 'selenium', parameters: [string(name: 'frontendDockerTag', value: dockerTag)]
        }    
        always {
          junit 'test-results/*.xml'
      }
    }
    

