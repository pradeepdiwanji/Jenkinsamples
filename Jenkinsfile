node {
 	// Clean workspace before doing anything
    deleteDir()

    try {
        stage ('Clone') {
        	checkout scm
        }
        stage ('Upload file') {
            steps {
                rtUpload (
                    // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
                    serverId: SERVER_ID,
                    spec: """{
                            "files": [
                                    {
                                        //"pattern": "jenkins-examples/pipeline-examples/resources/ArtifactoryPipeline.zip",
                                        "target": "local-repo-maven"
                                    }
                                ]
                            }"""
                )
            }
        }
        stage ('Build') {
        	sh "echo 'shell scripts to build project...'"
        }
        stage ('Tests') {
	        parallel 'static': {
	            sh "echo 'shell scripts to run static tests...'"
	        },
	        'unit': {
	            sh "echo 'shell scripts to run unit tests...'"
	        },
	        'integration': {
	            sh "echo 'shell scripts to run integration tests...'"
	        }
        }
      	stage ('Deploy') {
            sh "echo 'shell scripts to deploy to server...'"
      	}
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}




