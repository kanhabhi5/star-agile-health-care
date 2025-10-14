pipeline {
  agent any
          
  stages {
    stage('Git Checkout') {
      steps {
        echo 'This stage is to clone the repo from github'
        git branch: 'master', url: 'https://github.com/kanhabhi5/star-agile-health-care.git'
                        }
            }
    stage('Create Package') {
      steps {
        echo 'This stage will compile, test, package my application'
        sh 'mvn package'
                          }
            }
    
     stage('Create Docker Image') {
      steps {
        echo 'This stage will Create a Docker image'
        sh 'docker build -t kanhavisk/healthcare:1.0 .'
                          }
            }
     stage('Docker-Login') {
           steps {
              withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                                   }
                        }
                }

    stage('Docker Push-Image') {
      steps {
        echo 'This stage will push my new image to the dockerhub'
        sh 'docker push kanhavisk/healthcare:1.0'
            }
      } 
    stage('AWS-Login') {
      steps {
       withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'AWS-ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
         }
      }
    }
    stage('setting the Kubernetes Cluster') {
      steps {
        dir('terraform_files'){
          sh 'terraform init'
          sh 'terraform validate'
          sh 'terraform apply --auto-approve'
          sh 'sleep 20'
        }
      }
    }
    stage('deploy kubernetes'){
steps{
  sh 'sudo chmod 600 ./terraform_files/ubuntu.pem'    

  sh 'sudo scp -o StrictHostKeyChecking=no -i ./terraform_files/ubuntu.pem deployment.yml ubuntu@3.88.179.19:/home/ubuntu/'
  sh 'sudo scp -o StrictHostKeyChecking=no -i ./terraform_files/ubuntu.pem service.yml ubuntu@3.88.179.19:/home/ubuntu/'
script{
  try{
  sh 'ssh -o StrictHostKeyChecking=no -i ./terraform_files/sir.pem ubuntu@3.88.179.19 kubectl apply -f .'
  }catch(error)
  {
  sh 'ssh -o StrictHostKeyChecking=no -i ./terraform_files/sir.pem ubuntu@3.88.179.19 kubectl apply -f .'
  }
}
}
}
  }
}
