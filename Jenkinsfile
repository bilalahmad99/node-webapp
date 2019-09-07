#!/usr/bin/groovy

podTemplate(label: 'jenkins', containers: [
    containerTemplate(name: 'jnlp', image: 'lachlanevenson/jnlp-slave:3.10-1-alpine', args: '${computer.jnlpmac} ${computer.name}', workingDir: '/home/jenkins/agent', resourceRequestCpu: '200m', resourceLimitCpu: '300m', resourceRequestMemory: '256Mi', resourceLimitMemory: '512Mi'),
    containerTemplate(name: 'helm', image: 'lachlanevenson/k8s-helm:latest', command: 'cat', ttyEnabled: true),
],
volumes:[
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
]){

  node ('jenkins') {

    checkout scm

    stage ('test helm') {
      container('helm') {
        // Deploy using Helm chart
        echo 'env.PATH=' + env.PATH
        sh "helm list"
      }
    }

    stage ('deploy to k8s') {
      container('helm') {
        // Deploy using Helm chart
        sh "helm upgrade node-webapp-prod-release k8s-helm-chart/node-webapp/"
      }
    }
  }
}

