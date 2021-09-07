pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/jfrog/project-examples.git"
            }
        }

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: "JfrogArtifactory",
                    url: http://34.71.215.160:8082/artifactory,
                    credentialsId: 'admin_pradeep_artifactory'
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "JfrogArtifactory",
                    releaseRepo: 'sample-libs-release-local',
                    snapshotRepo: 'sample-libs-snapshot-local'
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "JfrogArtifactory",
                    releaseRepo: 'sample-libs-release-local',
                    snapshotRepo: 'sample-libs-snapshot-local'
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: MAVEN3, // Tool name from Jenkins configuration
                    pom: 'maven-examples/maven-example/pom.xml',
                    goals: 'clean install -U',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "JfrogArtifactory"
                )
            }
        }

        stage ('Xray scan') {
            steps {
                xrayScan (
                    serverId: "JfrogArtifactory",
                    failBuild: false
                )
            }
        }
    }
}