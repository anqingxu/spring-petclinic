pipeline {
  agent {
    kubernetes {
      label 'build-petclinic'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'builder.yaml'  // path to the pod definition relative to the root of our project
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
            container('kaniko') {
              script {
                sh "/kaniko/executor --dockerfile `pwd`/Dockerfile --context `pwd` --destination=632912596221.dkr.ecr.us-east-1.amazonaws.com/petclinic:v1.0.21"
                }
            }
          }
      }
  }
}
