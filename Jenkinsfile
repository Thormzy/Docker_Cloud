pipeline {
  agent any

  environment {
    imagename = "thormzy/docker-cloud"
    registryCredential = 'DockerHub'
    dockerImage = ''
  }

  stages {

    stage('Install Docker') {
      steps {
        sh 'curl -fsSL https://get.docker.com -o get-docker.sh'
        sh 'echo <password> | sudo -S sh get-docker.sh'
        sh 'echo <password> | sudo -S usermod -aG docker jenkins'
        sh 'echo <password> | sudo -S systemctl start docker'
      }
    }

    stage('Restart Jenkins') {
      steps {
        script {
          // Restart Jenkins
          Jenkins.instance.restart()
        }
      }
    }

    stage ('Build') {
      steps {
        sh 'mvn clean package'
        echo 'Maven Build'
      }
    }

    stage('Build Test') {
      steps {
        sh 'mvn test'
        echo 'Test Analysis'
      }
    }

    stage('Build Docker image') {
      steps {
        script {
          dockerImage = docker.build imagename + ":$BUILD_NUMBER"
        }
      }
    }

    stage('Push Docker Image to DockerHub') {
      steps {
        script {
          docker.withRegistry('', registryCredential) {
            dockerImage.push("$BUILD_NUMBER")
          }
        }
      }
    }

    stage('Remove Unused docker image') {
      steps {
        sh "docker rmi $imagename:$BUILD_NUMBER"
      }
    }
  }
}
