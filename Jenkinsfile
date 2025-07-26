node {
    def mvnHome = tool 'MVN_HOME'

    env.JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
    env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
    
    env.NEXUS_VERSION = 'nexus3'
    env.NEXUS_PROTOCOL = 'http'
    env.NEXUS_URL = '204.236.252.196:8081'
    env.NEXUS_REPOSITORY = 'spring3'
    env.NEXUS_CREDENTIAL_ID = 'Nexus_server'

    boolean isSuccess = false

    try {
        stage('Clone Code') {
            git url: 'https://github.com/Syedshakeel23/spring3-mvc-maven-xml-hello-world-1.git'
        }

        stage('Build WAR') {
            sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore=true clean install"
        }

        stage('Upload to Nexus') {
            def groupId = "com.ncodeit"
            def artifactId = "ncodeit-hello-world"
            def version = "${BUILD_NUMBER}"
            def packaging = "war"

            nexusArtifactUploader(
                nexusVersion: env.NEXUS_VERSION,
                protocol: env.NEXUS_PROTOCOL,
                nexusUrl: env.NEXUS_URL,
                groupId: groupId,
                artifactId: artifactId,
                version: version,
                repository: env.NEXUS_REPOSITORY,
                credentialsId: env.NEXUS_CREDENTIAL_ID,
                artifacts: [
                    [artifactId: artifactId, classifier: '', file: "target/${artifactId}.${packaging}", type: packaging],
                    [artifactId: artifactId, classifier: '', file: "pom.xml", type: "pom"]
                ]
            )
        }

        isSuccess = true
    } catch (e) {
        echo "Build failed: ${e}"
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        if (isSuccess) {
            stage('Success') {
                echo "🎉 Build and Upload to Nexus completed successfully!"
            }
        }
    }
}
