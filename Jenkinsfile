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
                    id: "localhost",
                    url: http://localhost:8082/artifactory,
                    credentialsId: admin_pradeep_artifactory
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "localhost",
                    releaseRepo: ARTIFACTORY_LOCAL_RELEASE_REPO,
                    snapshotRepo: ARTIFACTORY_LOCAL_SNAPSHOT_REPO
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "localhost",
                    releaseRepo: ARTIFACTORY_VIRTUAL_RELEASE_REPO,
                    snapshotRepo: ARTIFACTORY_VIRTUAL_SNAPSHOT_REPO
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: Maven3, // Tool name from Jenkins configuration
                    pom: 'maven-examples/maven-example/pom.xml',
                    goals: 'clean install',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "localhost"
                )
            }
        }
    }
}