pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh 'mvn clean package -DskipTests=true'
              archive 'target/*.jar' //so that they can be downloaded later
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
            sh 'docker build -t pprasant/devsecops:""$GIT_COMMIT"" .'
            sh 'docker push pprasant/devsecops:""$GIT_COMMIT""'

          }

        }

      }
    }
}

