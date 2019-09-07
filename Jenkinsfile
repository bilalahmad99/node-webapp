#!/usr/bin/groovy

podTemplate(label: 'jenkins', containers: [
    containerTemplate(name: 'jnlp', image: 'lachlanevenson/jnlp-slave:3.10-1-alpine', args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins', resourceRequestCpu: '200m', resourceLimitCpu: '300m', resourceRequestMemory: '256Mi', resourceLimitMemory: '512Mi'),
    containerTemplate(name: 'docker', image: 'docker:1.12.6', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.4.8', command: 'cat', ttyEnabled: true)
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
]){

  node ('jenkins') {

    checkout scm

    stage ('test docker') {
      container('docker') {
        withEnv(['PATH+EXTRA=/usr/sbin:/usr/bin:/sbin:/bin']) {
          sh "docker ps -a"
        }
      }
    }

    stage ('test helm') {
      container('helm') {
        // Deploy using Helm chart
        sh "helm list"
      }
    }

    stage ('deploy to k8s') {
      container('helm') {
        // Deploy using Helm chart
        sh "helm upgrade node-webapp node-webapp/k8s-helm-chart/node-webapp/"
      }
    }
  }
}

