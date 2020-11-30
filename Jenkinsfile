@Library('piper-lib-os') _

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
      def status = cpiDeployIntegrationDesigntimeArtifact script: this
    if (status == null){
       def error = cpiGetiFlowMplStatus script: this
	   
	   if (error == null){
		   
		  error = cpiDownloadiFlowArtifact script: this
		  def folder=commonPipelineEnvironment.configuration.general.iflowName
		  fileOperations([fileUnZipOperation(filePath: folder + ".zip", targetLocation: folder)])

		 def files = findFiles excludes: '', glob: folder + '/**/*';
			 for (def file : files) {
				  def filecontent = readFile encoding: 'Base64', file: file.path;
				  def payload ='{\"message\":\"dirval\",\"content\":\"contentval\",\"committer\":{\"name\":\"Mayur Belur Mohan\",\"email\":\"mayur.belur.mohan@sap.com\"}}';
				  payload =payload.replace('dirval',folder);
				  payload =payload.replace('contentval',filecontent);
				  def resp = httpRequest customHeaders: [[maskValue: false, name: 'rejectUnauthorized', value: 'false'], [maskValue: false, name: 'authorization', value: 'Basic STA1MDM2ODozNDViMTNmZWM5NDc0Y2ZhOTFjNDk4ZTE2MGYwMDVhNGUxYzQyOTFi']], httpMode: 'PUT', requestBody: payload, responseHandle: 'LEAVE_OPEN', url: 'https://github.wdf.sap.corp//api/v3/repos/I050368/IO-PM/contents/' + file.path
				  println 'github response:-' + resp.content;
				  resp.close();
				 }
		 //clean up activities
			println 'initiate workspace cleanup';
			fileOperations([fileDeleteOperation(excludes: '', includes: folder + ".zip")])
	   }

    }
  }
}
