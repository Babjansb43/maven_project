pipeline {
    agent any
    
    parameters {
        choice(choices: ['dev', 'test', 'prod'], description: 'Select the environment to deploy', name: 'ENVIRONMENT')
    }

    environment {
        PROD_CRED   = credentials('prod-credentials')
        TEST_CRED   = credentials('test-credentials')
        APP_NAME    = "spark-java"
        DEPLOY_PATH = "/opt/deployment"
    }
              
    tools {
        maven 'Maven' 
    }
    
    stages {
         stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Babjansb43/maven_project.git'
            }
        }
        stage('Parallel stages') {
            steps {
                script {
                  Parallel[
                    {
                       stage('Unit Test') {
                         echo "Doing unit tests"
                      },
                       stage('Static code') {
                         echo "Doing static code analysis"
                      },
                       stage('security') {
                         echo "Doing security scan"
                      }
                    }
                  ]
                }
            }
        }
        stage('Build') {
            steps {
               sh 'mvn clean package'
            }
        }
        stage('Verify') {
            steps {
                echo 'Build completed succesfully'
            }
        }
        stage('Archiving') {
            steps {
               archiveArtifacts artifacts: 'target/*.war', fingerprint: true, followSymlinks: false, onlyIfSuccessful: true
            }
        }
        stage('Environment Test') {
            steps {
                echo "Application Name: ${APP_NAME}"
                echo "Deploy path: ${DEPLOY_PATH}"
            }
        }
        stage("Deploy to Dev") {
            when {
                expression { params.ENVIRONMENT == 'dev' }
            }
            steps {
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
        stage("Deploy to test") {
            when {
                expression { params.ENVIRONMENT == 'test' }
            } 
            steps {
                echo "Deploying with user ${TEST_CRED}"
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
        stage("Deploy to Prod") {
            when {
                expression { params.ENVIRONMENT == 'prod' }
            }
            steps {
                echo "Deploying with user ${PROD_CRED}"
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
    }
      post {
        always {
            cleanWs()
        }
    }
}