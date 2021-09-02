/*node {
 	// Clean workspace before doing anything
    deleteDir()

    try {
        stage ('Clone') {
        	checkout scm
        }
         stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "localhost",
                    url: 'http://localhost:8082/artifactory',
                    credentialsId: 'admin_pradeep_artifactory'
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "localhost",
                    releaseRepo: 'local-repo-maven',
                    snapshotRepo: 'local-repo-maven'
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "localhost",
                    releaseRepo: 'local-repo-maven',
                    snapshotRepo: 'local-repo-maven'
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: MAVEN_TOOL, // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
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
}*/

pipeline {
    agent any
    environment {
        CRED_ID = 'admin_pradeep_artifactory'
        ARTIFACTORY_SERVER_ID = 'localhost'
        ARTIFACTORY_DEPLOYER_RELEASE_REPO = 'local-repo-maven'
        ARTIFACTORY_DEPLOYER_SNAPSHOT_REPO = 'local-repo-maven'
        ARTIFACTORY_RESOLVER_RELEASE_REPO = 'local-repo-maven'
        ARTIFACTORY_RESOLVER_SNAPSHOT_REPO = 'local-repo-maven'

    }
    stages {
        stage ('Maven container config') {
            stages {
                stage ('Artifactory configuration') {
                    steps {
                        rtMavenDeployer(
                                id: "MAVEN_DEPLOYER",
                                serverId: env.ARTIFACTORY_SERVER_ID,
                                releaseRepo: env.ARTIFACTORY_DEPLOYER_RELEASE_REPO,
                                snapshotRepo: env.ARTIFACTORY_DEPLOYER_SNAPSHOT_REPO
                        )

                        rtMavenResolver(
                                id: "MAVEN_RESOLVER",
                                serverId: env.ARTIFACTORY_SERVER_ID,
                                releaseRepo: env.ARTIFACTORY_RESOLVER_RELEASE_REPO,
                                snapshotRepo: env.ARTIFACTORY_RESOLVER_SNAPSHOT_REPO
                        )
                    }
                }
                stage ('Publish BuildInfo') {
                    steps {
                        rtBuildInfo (
                                captureEnv: true
                        )
                        rtPublishBuildInfo (
                                serverId: env.ARTIFACTORY_SERVER_ID
                        )
                    }
                }
                stage('Build') {
                    steps {
                        rtMavenRun (
                                pom: 'pom.xml',
                                goals: '-B -DskipTests clean package',
                                deployerId: "MAVEN_DEPLOYER",
                                resolverId: "MAVEN_RESOLVER"
                        )
                    }
                }
            }
        }
    }
}




