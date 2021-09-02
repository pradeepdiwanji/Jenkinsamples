node {
    // Clean workspace before doing anything
    deleteDir()

    try {
        stage ('Clone') {
            checkout scm
        }
        stage ('Build') {
           // sh "echo 'shell scripts to build project...'"
            sh 'make' 
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
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
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}