pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git branch: 'master', url: "https://github.com/cameronmcnz/rock-paper-scissors.git"
            }
        }

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: 'localhost',
                    url: 'http://localhost:8082/artifactory',
                    credentialsId: 'admin_pradeep_artifactory'
                )

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: 'localhost',
                    releaseRepo: 'local-repo-maven',
                    snapshotRepo: 'local-repo-maven'
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: 'localhost',
                    releaseRepo: 'local-repo-maven',
                    snapshotRepo: 'local-repo-maven'
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
                    serverId: 'localhost'
                )
            }
        }
    }
}