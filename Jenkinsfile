#!/usr/bin/env groovy
GString POD_LABEL = "jnowalczyk-${UUID.randomUUID().toString()}"

pipeline {

  agent {
    kubernetes {
      label POD_LABEL
      yaml """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: builder
    image: docker
    command:
    - cat
    tty: true
    volumeMounts:
    - name: docker
      mountPath: /var/run/docker.sock
  volumes:
  - name: docker
    hostPath:
      path: /var/run/docker.sock
"""
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
    timeout(time: 2, unit: 'HOURS')
  }

  stages {
      
    stage('Build') {
      environment {
        DOCKER_REGISTRY = 'artifactory.datapwn.com'
        DOCKER_IMAGE = 'tlnd-docker-dev/talend/sandbox/infra-aws'
      }
      steps {
        container (name: 'builder') {
          withCredentials([usernamePassword(credentialsId: 'artifactory-datapwn-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_LOGIN')]) {
            sh '''#!/bin/bash
	    set +x
            cd docker/
            cat Dockerfile
            '''
          }
        }
      }
    }
  }
}
