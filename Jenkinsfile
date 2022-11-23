pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        registry = "120761001082.dkr.ecr.us-east-2.amazonaws.com/my-repo1"
    }

    stages {
        stage('Code Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/swagat030/springboot-app-main.git']]])
            }
        }

        stage ('Maven Build') {
          steps {
            sh 'mvn clean install'
            }
        }

        stage('Building image') {
          steps{
            script {
              dockerImage = docker.build registry
            }
          }
        }

       stage('Pushing to ECR') {
          steps{
            script {
               sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 120761001082.dkr.ecr.us-east-2.amazonaws.com'
               sh 'docker push 120761001082.dkr.ecr.us-east-2.amazonaws.com/my-repo1:latest'
            }
          }
       }

       stage('K8S Deploy') {
          steps{
            script {
               withKubeConfig([credentialsId: 'k8s', serverUrl: '']) {
               sh ('kubectl apply -f  eks-deploy-k8s.yaml')
               }
            }
          }
       }

    }

}
