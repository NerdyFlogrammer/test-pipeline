pipeline {
  environment { 
        registry = "nerdyflogrammer/demo" 
      }
  agent {
    kubernetes {
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'build-pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'maven'  // define a default container if more than a few stages use it, will default to jnlp container
    }
  }
  stages {
    stage('Build') {
      steps {  // no container directive is needed as the maven container is the default
        sh "mvn clean package"   
      }
    }
    stage('Build Docker Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub_id', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          container('docker') {  
            sh "docker build -t $registry:dev ."  // when we run docker in this step, we're running it via a shell on the docker build-pod container, 
            sh "docker login --username=$DOCKER_USER --password=$DOCKER_PASS && docker push -t $registry:dev"        // which is just connecting to the host docker deaemon
          }
        }
      }
    }
  }
}
