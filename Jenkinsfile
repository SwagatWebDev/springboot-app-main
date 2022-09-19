pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        registry = "120761001082.dkr.ecr.us-east-2.amazonaws.com/my-repo"
    }

    stages {
        stage('Code Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/swagat030/angular-springboot-mysql-k8s.git']]])
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
               sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin account_id.dkr.ecr.us-east-2.amazonaws.com'
               sh 'docker push account_id.dkr.ecr.us-east-2.amazonaws.com/my-docker-repo:latest'
            }
          }
       }

       stage('K8S Deploy') {
          steps{
            script {
               withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
               sh ('kubectl apply -f  eks-deploy-k8s.yaml')
               }
            }
          }
       }

    }

}
