pipeline {
  environment {
    ip = 'ec2-54-197-239-189.compute-1.amazonaws.com'
    imageName = 'innex42/cw2'
    image = ''
    version = ''
  }

  agent any

  stages {
    stage('Docker image') {
      steps {
        script {
          version = "$BUILD_NUMBER"
          
          image = docker.build(imageName + ':' + version)
        }
      }
    }

    stage('Docker Hub') {
      steps {
        script{
          docker.withRegistry( '' , 'docker-hub-credentials') {
          image.push()
        }
        }
      }
    }

    stage('Test') {
      steps {
        script{
         sh "docker run --name app-test -p 3000:8080 -d $imageName:$BUILD_NUMBER"
        sleep(time: 1, unit: 'SECONDS')
        sh "curl $ip:3000"
        sh 'docker stop app-test && docker rm app-test'
        }
      }
    }

    stage('Update deployment') {
      steps {
        script{
         sh "ssh -o StrictHostKeyChecking=no ubuntu@$ip kubectl set image deploy/cw2 cw2=$imageName:$version"

        }
      }
    }
  }
}
