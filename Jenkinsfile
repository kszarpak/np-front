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
          sh "pip3 install -r requirements.txt"
          sh "python3 -m pytest --cov=. --cov-report xml:test-results/coverage.xml --junitxml=test-results/pytest-report.xml"
        }
      }

        stage('Sonarqube analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Build application image') {
            steps {
                script {
                  // Prepare basic image for application
                  dockerTag = "${env.BUILD_ID}.${env.GIT_COMMIT.take(7)}"
                  applicationImage = docker.build("kornzysiek/frontend:$dockerTag",".")
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
        always {
          junit 'test-results/*.xml'
      }
    }
    
}
