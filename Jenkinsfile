pipeline {
  agent {
    kubernetes {
      label 'build-petclinic'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }

  triggers {
    pollSCM 'H/2 * * * *'
  }

  options {
    buildDiscarder logRotator(
      daysToKeepStr: '16',
      numToKeepStr: '10'
    )
  }

  stages {
      stage('Build') {
          steps {  // no container directive is needed as the maven container is the default
            sh "mvn -B package -DskipTests"
          }
      }
      stage(' Unit Testing') {
        steps {
          sh """
          echo "Running Unit Tests"
          """
        }
      }

      stage('Code Analysis') {
        steps {
          sh """
          echo "Running Code Analysis"
          """
        }
      }

      stage('Build and publish Docker Image') {
          steps {
            container('docker') {
              //https://www.brightbox.com/blog/2018/01/22/push-builds-to-dockerhub/
              withDockerRegistry([ credentialsId: "dockerhub_id", url: "" ]) {
                sh "docker build -t anqingxu/petclinic:v1.0.17 ."
                sh "docker push anqingxu/petclinic:v1.0.17"
              }
            }
          }
      }
  }
}
