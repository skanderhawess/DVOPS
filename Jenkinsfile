pipeline {
 agent any

  environment {
    IMAGE = "skanderhawess/student-management:1.0"
    SONAR_ENV = "SonarQube"
    SONAR_PROJECT_KEY = "student-management"
    KUBECONFIG = "/var/lib/jenkins/.kube/config"
  }

  stages {
    stage('Build JAR') {
      steps {
        sh './mvnw -B clean package -DskipTests'
      }
    }
    stage('DEBUG Docker perms') {
  steps {
    sh '''
      whoami
      id
      groups
      ls -l /var/run/docker.sock
      docker version || true
    '''
  }
}


    stage('Build Docker image') {
      steps {
       sh "docker build -t ${IMAGE} ."
       sh "docker push ${IMAGE}"
      }
    }

    stage('Docker Login + Push') {
  steps {
    withCredentials([usernamePassword(
      credentialsId: 'dockerhub-creds',
      usernameVariable: 'DOCKER_USER',
      passwordVariable: 'DOCKER_PASS'
    )]) {
      sh '''
        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
        docker push skanderhawess/student-management:1.0
      '''
    }
  }
}

  }
}
