pipeline {
    agent {
        label 'agent'
    }

    environment {
        PIP_BREAK_SYSTEM_PACKAGES = 1
    }
    
    stages {
      stage('Get code') {
        steps {
         checkout scm 
        }
      }
    }

    stage ('Run test') {
        steps {
            sh "pip3 install -r requirements.txt"
            sh "python3 -m pytest --cov=. --cov-report xml:test-results/coverage.xml --junitxml=test-results/pytest-repoert.xml"
        }
    }
    
}
