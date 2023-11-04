pipeline {
  agent any
  stages {
    stage('Build Artifact') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archiveArtifacts artifacts: 'target/*.jar', onlyIfSuccessful: true
      }
    }
    stage('SonarQube - SAST') {
      steps {
        sh "mvn sonar:sonar -Dsonar.projectKey=secops-application -Dsonar.host.url=http://192.168.29.49:9000 -Dsonar.login=sqp_3e34c8b75cb99548f20adab86af9700db083cc22"
      }
    }
  }
}
