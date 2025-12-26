pipeline {
 agent any

  environment {
    IMAGE = "skanderhawess/student-management:1.0"
    SONAR_ENV = "SonarQube"
    SONAR_PROJECT_KEY = "student-management"
    KUBECONFIG = "/var/lib/jenkins/.kube/config"
   SONAR_AUTH_TOKEN = credentials('sonarqube-token')
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
          sh """
            ./mvnw -B sonar:sonar \
              -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
              -Dsonar.projectName=${SONAR_PROJECT_KEY} \
              -Dsonar.host.url=http://192.168.56.10:9000 \
              -Dsonar.login=$SONAR_AUTH_TOKEN
          """
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
