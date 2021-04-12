pipeline {
  agent {
    docker {
     image 'node:10.1-alpine'
     args '-p 8001:8001'
    }
  }
  environment {
    CI = 'true'
    HOME = '.'
    npm_config_cache = 'npm-cache'
  }
  stages {
    stage('Checkout External Project'){
      step{
      git 'https://github.com/muzafferjoya/reading-app.git'
    }
  }
    stage('Install Packages') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test and Build') {
      parallel {
        stage('Run Tests') {
          steps {
            sh 'npm run test'
          }
        }
        stage('Create Build Artifacts') {
          steps {
            sh 'npm run build'
          }
        }
      }
    }

stage('Deployment on S3 Bucket') {
  steps {
    withAWS(region:'us-east-1',credentials:'muzaffar-aws-id') {
    s3Delete(bucket: 'muzaffar-khan/develop', path:'**/*');
    s3Upload(bucket: 'muzaffar-khan/develop', workingDir:'dist', includePathPattern:'**/*', excludePathPattern:'.git/*, **/node_modules/**');
            }
          }
        }
}
post {

         failure {
            emailext attachLog: true,
             body: '''${SCRIPT, template="groovy-html.template"}''', 
             subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
             mimeType: 'text/html',to: "muzafferjoya@gmail.com"
          }
         success {
            emailext attachLog: true,
             body: '''${SCRIPT, template="groovy-html.template"}''', 
             subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
             mimeType: 'text/html',to: "muzafferjoya@gmail.com"
          }     
    }
}
