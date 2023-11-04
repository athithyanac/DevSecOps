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
    stage('Docker Build and Push') {
      steps {
        script {
          // Define the Docker image name with the GIT_COMMIT as the tag
          def dockerImageName = "athithyanac/node-service:${env.GIT_COMMIT}"

          // Authenticate with Docker Hub and push the image
          withDockerRegistry(credentialsId: "Dockerhub", url: "https://index.docker.io/v1/") {
            sh "docker build -t ${dockerImageName} ."
            sh "docker push ${dockerImageName}"
          }
        }
      }
    }
    stage('Kubernetes Deployment - DEV') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig']) {
          sh "cp k8s_deployment_service.yaml k8s_deployment_service_temp.yaml"
          sh "sed -i 's#replace#dsocouncil/node-service:${GIT_COMMIT}#g' k8s_deployment_service_temp.yaml"
          sh "kubectl apply -f k8s_deployment_service_temp.yaml"
          sh "rm k8s_deployment_service_temp.yaml"
        }
      }
    }
  }
}
