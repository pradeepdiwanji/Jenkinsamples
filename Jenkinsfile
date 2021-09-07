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
                    id: 'JfrogArtifactory',
                    url: 'http://34.71.215.160:8082/artifactory',
                    credentialsId: 'admin_pradeep_artifactory'
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: 'JfrogArtifactory',
                    releaseRepo: 'sample-libs-release-local',
                    snapshotRepo: 'sample-libs-snapshot-local'
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: 'JfrogArtifactory',
                    releaseRepo: 'sample-libs-release-local',
                    snapshotRepo: 'sample-libs-snapshot-local'
                )
              }  
        }


        stage ('Exec Maven') {
         environment {
                   //MAVEN_HOME = '/usr/local/Cellar/maven/3.8.2/libexec'
                   JAVA_HOME= '/Applications/Eclipse JEE.app/Contents/Eclipse/plugins/org.eclipse.justj.openjdk.hotspot.jre.full.macosx.x86_64_16.0.1.v20210528-1205/jre'
                   }
            steps {
                rtMavenRun (
                    tool: 'Maven3', // Tool name from Jenkins configuration
                    pom: 'maven-examples/maven-example/pom.xml',
                    goals: 'clean compile install -DskipTests',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: 'JfrogArtifactory'
                )
            }
        }
    }
    }
}