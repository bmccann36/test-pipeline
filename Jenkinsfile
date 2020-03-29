pipeline {
  agent {
    kubernetes {
      label 'promo-app'  // all your pods will be named with this prefix, followed by a unique id
      defaultContainer 'maven' 
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:  # list of containers that you want present for your build, you can define a default container in the Jenkinsfile
    - name: maven
      image: maven:3.5.4-jdk-8-slim
      command: ["tail", "-f", "/dev/null"]  # this or any command that is bascially a noop is required, this is so that you don't overwrite the entrypoint of the base container
      imagePullPolicy: Always # use cache or pull image for agent
      resources:  # limits the resources your build contaienr
        requests:
          memory: "1Gi"
          cpu: "500m"
        limits:
          memory: "1Gi"
    - name: docker
      image: docker:18.06.1
      command: ["tail", "-f", "/dev/null"]
      imagePullPolicy: Always
      volumeMounts:
        - name: docker
          mountPath: /var/run/docker.sock # We use the k8s host docker engine
  volumes:
    - name: docker
      hostPath:
        path: /var/run/docker.sock
"""
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
        container('docker') {  
          sh "docker build -t vividlukeloresch/promo-app:dev ."  // when we run docker in this step, we're running it via a shell on the docker build-pod container, 
          println("really????")
        }
      }
    }
  }
}
