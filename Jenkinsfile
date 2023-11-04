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
        sh "mvn sonar:sonar -Dsonar.projectKey=secops-application -Dsonar.host.url=http://192.168.29.49:9000 -Dsonar.token=sqp_625fc5bb1983ec8736fc5b6bdca046fa67e54677"
      }
    }
    stage('SCA Scan - Dependency-Check ') {
      steps {
        sh "mvn dependency-check:check"
      }
      post {
        always {
          dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
        }
      }
    }
  }
}
