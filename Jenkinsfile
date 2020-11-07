pipeline {
  agent {
    kubernetes {
      label 'petclinic'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  environment {
    registry = '632912596221.dkr.ecr.us-east-1.amazonaws.com'
    registryCredential = 'ecr_id'
    dockerImage = ''
  }
  stages {
    stage('Build') {
      steps {  // no container directive is needed as the maven container is the default
        sh "mvn -B package -DskipTests"
      }
    }
    stage('Build Docker Image') {
      steps {
        container('docker') {
          //https://www.brightbox.com/blog/2018/01/22/push-builds-to-dockerhub/
          //withDockerRegistry([ credentialsId: "dockerhub_id", url: "" ]) {
          //  sh "docker build -t anqingxu/petclinic:v1.0.2 ."
          //  sh "docker push anqingxu/petclinic:v1.0.2"
          //}

          script{
            //https://stackoverflow.com/questions/59084989/push-to-ecr-from-jenkins-pipeline
            dockerImage = docker.build registry + "/anqingxu/petclinic:v1.0.2"

            cleanup current user docker credentials
            sh 'rm -rf ~/.dockercfg || true'
            sh 'rm -rf ~/.docker/config.json || true'

            docker.withRegistry("https://" + registry, "ecr:us-east-1:" + registryCredential) {
              sh 'cat ~/.dockercfg'
              sh 'cat ~/.docker/config.json'
              dockerImage.push()
            }
          }
        }
      }
    }
  }
}
