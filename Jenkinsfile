pipeline {
 agent any
 options {
    disableConcurrentBuilds()
    timeout(time: 30, unit: 'MINUTES')
  }

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
   stage('MVN SONARQUBE') {
  steps {
    withSonarQubeEnv("${SONAR_ENV}") {
      withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
        sh """
          ./mvnw -B sonar:sonar \
            -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
            -Dsonar.projectName=${SONAR_PROJECT_KEY} \
            -Dsonar.host.url=$SONAR_HOST_URL \
            -Dsonar.token=$SONAR_TOKEN
        """
      }
    }
  }
}
    stage('Quality Gate') {
      steps {
        timeout(time: 10, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }



    stage('Build Docker image') {
      steps {
       sh "docker build -t ${IMAGE} ."
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
