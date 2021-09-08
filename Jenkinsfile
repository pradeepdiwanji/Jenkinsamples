node {
    def server
    def rtMaven
    def buildInfo

    stage ('Clone') {
        git url: 'https://github.com/jfrog/project-examples.git'
    }

    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
        server = Artifactory.server 'JfrogArtifactory'

        rtMaven = Artifactory.newMavenBuild()
        // Tool name from Jenkins configuration
        rtMaven.tool = 'Maven3'
        
        rtMaven.deployer releaseRepo: 'sample-libs-release-local', 
        snapshotRepo: 'sample-libs-snapshot-local', 
        server: server
        
        rtMaven.resolver releaseRepo: 'sample-libs-release', 
        snapshotRepo: 'sample-libs-release', 
        server: server 
        
        buildInfo = Artifactory.newBuildInfo()
    }

    stage ('Exec Maven') {
        rtMaven.run pom: 'maven-examples/maven-jib-example/pom.xml', 
        goals: 'clean install', 
        buildInfo: buildInfo
    }

    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }

    stage ('Xray scan') {
        def scanConfig = [
                'buildName'      : buildInfo.name,
                'buildNumber'    : buildInfo.number,
                'failBuild'      : true
        ]
        def scanResult = server.xrayScan scanConfig
        echo scanResult as String
    }
}