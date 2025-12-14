pipeline {
  agent { label 'ssh' }  // ou le label de ton agent (ex: agent-ssh)

  environment {
    IMAGE = "skanderhawess/student-management:1.0"
  }

  stages {
    stage('Build JAR') {
      steps {
        sh './mvnw -B clean package -DskipTests'
      }
    }

    stage('Build Docker image') {
      steps {
        sh 'docker build -t $IMAGE .'
      }
    }

    stage('Docker Login + Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
          sh 'docker push $IMAGE'
        }
      }
    }
  }
}
