properties([
  parameters([
    choice(choices: ['java', 'python', 'go', 'c', 'javascript'], description: 'Select the language:', name: 'language')
  ])
])

pipeline {
  agent any
  environment {
    registry = "450003755604.dkr.ecr.us-east-2.amazonaws.com/compilationengine/${language}"
  }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shubham14da/jenkins-ECR.git'
            }
        }
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Pushing to ECR') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'jenkins-aws-ecr', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        sh 'aws ecr get-login-password --region us-east-2 | docker login --username AWS --password-stdin 450003755604.dkr.ecr.us-east-2.amazonaws.com'
                        sh "docker push ${registry}:latest"
                    }
                }
            }
        }
    

             stage('stop previous containers') {
               steps {
            sh 'docker ps -f name=mypythonContainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mypythonContainer -q | xargs -r docker container rm'
         }
       }
            stage('Docker Run') {
              steps{
                   script {
                sh 'docker run -d -p 8096:5000 --rm --name mypythonContainer 450003755604.dkr.ecr.us-east-2.amazonaws.com/compilationengine/python:latest'     
      }
    }
        }
    }
  }
