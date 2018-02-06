node {
    def root = tool name: 'Go1.9', type: 'go'
    ws("${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/src/github.com/skusol/upload") {
        withEnv(["GOROOT=${root}", "GOPATH=${JENKINS_HOME}/jobs/${JOB_NAME}/builds/${BUILD_ID}/", "PATH+GO=${root}/bin"]) {
            env.PATH="${GOPATH}/bin:$PATH"
            
            stage 'Checkout'
        
            git url: 'https://github.com/skusol/go-upload.git'
        
            
            stage 'Test'
            sh 'go vet'
            sh 'go test -cover'
            
            stage 'Build'
            sh 'go build .'

  stage("Archive artifacts") {
    // Archive the binary files in Jenkins so we can retrieve them later should we need to audit them
    archiveArtifacts artifacts: 'bin/**', fingerprint: true
  }

	stage("Publish to Artifactory") {
    // create a Artifactory server reference with some credentials we stored in Jenkins already
    def server = Artifactory.server 'R0001'
    // Upload spec is a definition for the Artifactory plugin to tell it how and what to upload, and where in Artifactory it should go
    def uploadSpec = """{
      "files": [
        {
          "pattern": "bin/*",
          "target": "generic-local/golang/upload/",
          "flat": false
        }
     ]
    }"""
    // perform the upload
    server.upload(uploadSpec)
  }
            
            stage 'Deploy'
            // Do nothing.
        }
    }
}
