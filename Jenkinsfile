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
    SONAR_AUTH_TOKEN = credentials('sonarqube-token')
  }

  stages {

    stage('Build JAR') {
      steps {
        sh './mvnw -B clean package -DskipTests'
      }
    }

    stage('MVN SONARQUBE') {
      steps {
        withSonarQubeEnv("${SONAR_ENV}") {
          sh '''
            ./mvnw -B org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
              -Dsonar.projectKey=student-management \
              -Dsonar.projectName=student-management \
              -Dsonar.host.url=http://192.168.56.10:9000 \
              -Dsonar.token=$SONAR_AUTH_TOKEN
          '''
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
