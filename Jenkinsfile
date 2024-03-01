pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh 'mvn clean package -DskipTests=true'
              archive 'target/*.jar'
            }
        }   

      stage('Unit Tests') {
        steps {
          sh 'mvn test'
        }

        post {
          always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
          }
        }
      }

      stage('Docker build and Push') {
        steps {

          withDockerRegistry([credentialsId: "dockerhub-credential", url: ""]) {
            sh 'printenv'
            sh "docker build -t pprasant/devsecops:${BUILD_ID} ."
            sh "docker push pprasant/devsecops:${BUILD_ID}"

          }

        }

      }

      stage('Kubernetes Deployment - DEV') {
        steps {
          withKubeConfig([credentialsId: 'Kubeconfig']) {
            sh "sed -i 's#replace#pprasant/devsecops:${BUILD_ID}#g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }
    }
}

