node {
    stage('Clone Code') {
        git 'https://github.com/Syedshakeel23/spring3-mvc-maven-xml-hello-world-1.git'
    }

    stage('Build WAR') {
        // Define Maven tool
        def mvnHome = tool name: 'MVN_HOME', type: 'hudson.tasks.Maven$MavenInstallation'

        // Use mvnHome/bin/mvn
        sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore=true clean install"
    }

    stage('Upload Artifact to Nexus') {
        nexusArtifactUploader(
            nexusVersion: 'nexus3',
            protocol: 'http',
            nexusUrl: '204.236.252.196:8081',
            groupId: 'com.mycompany',
            version: '16',
            repository: 'spring3',
            credentialsId: 'Nexus_server',
            artifacts: [
                [artifactId: 'spring3-mvc-maven-xml-hello-world',
                 classifier: '',
                 file: 'target/ncodeit-hello-world.war',
                 type: 'war'],
                [artifactId: 'spring3-mvc-maven-xml-hello-world',
                 classifier: '',
                 file: 'pom.xml',
                 type: 'pom']
            ]
        )
    }
}
