pipeline {
    agent any
    
    parameters {
        choice(choices: ['dev', 'test', 'prod'], description: 'Select the environment to deploy', name: 'ENVIRONMENT')
      }
              
    tools {
        maven 'Maven' 
    }
    
    stages {
         stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Babjansb43/maven_project.git'
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
        stage("Deploy to Dev") {
            when {
                expression { params.Environment == 'dev' }
            }
            steps {
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
        stage("Deploy to test") {
            when {
                expression { params.Environment == 'test' }
            }
            steps {
      withCredentials([
        usernamePassword(
        credentialsId: 'test-credentials', 
        passwordVariable: 'DEPLOY_PASSWORD', 
        usernameVariable: 'DEPLOY_USER'
       )
     ])  
        {
                echo "Deploying with user ${DEPLOY_USER}"
     }
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
        stage("Deploy to Prod") {
            when {
                expression { params.Environment == 'prod' }
            }
            steps {  
                   
      withCredentials([
        usernamePassword(
        credentialsId: 'prod-credentials', 
        passwordVariable: 'DEPLOY_PASSWORD', 
        usernameVariable: 'DEPLOY_USER'
       )
     ])
        {
                echo "Deploying with user ${DEPLOY_USER}"
    }
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