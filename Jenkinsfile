@Library('piper-lib-os@cpiDeployIntegrationDesigntimeArtifact') _

node() {
  stage('init') {
    deleteDir()
    checkout scm
  }
  stage('prepare piper') {
    dockerExecuteOnKubernetes(script: this, dockerImage: 'golang:1.15') {
      sh """|#!/bin/bash -e
            |git clone https://github.com/mayurmohan/jenkins-library piperlib
            |cd piperlib
            |git checkout origin/cpiDeployIntegrationDesigntimeArtifact
            |go build -o piper .
            |mv piper ..
            |cd -
            |rm -rf piperlib
         """.stripMargin()
      stash name: 'piper-bin', includes: 'piper'
      sh "rm piper"
       
    }
  }
  stage('cpiDeployIntegrationDesigntimeArtifact Command') {
      cpiDeployIntegrationDesigntimeArtifact script: this
  }
}
