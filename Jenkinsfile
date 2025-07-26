node {
    // Set environment variables
    def JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
    def PATH = "${JAVA_HOME}/bin:${env.PATH}"
    def MVN_HOME = tool name: 'MVN_HOME' // This must match Jenkins tool name
    def NEXUS_VERSION = "nexus3"
    def NEXUS_PROTOCOL = "http"
    def NEXUS_URL = "204.236.252.196:8081"
    def NEXUS_REPOSITORY = "spring3"
    def NEXUS_CREDENTIAL_ID = "Nexus_server"

    stage("Clone Code") {
        git url: 'https://github.com/Syedshakeel23/spring3-mvc-maven-xml-hello-world-1.git'
    }

    stage("Build WAR") {
        withEnv(["JAVA_HOME=${JAVA_HOME}", "PATH=${PATH}"]) {
            sh "${MVN_HOME}/bin/mvn -Dmaven.test.failure.ignore=true clean install"
        }
    }

    stage("Publish to Nexus") {
        script {
            def pom = readMavenPom file: 'pom.xml'
            def files = findFiles(glob: "target/*.${pom.packaging}")

            if (files.length == 0) {
                error "No ${pom.packaging} file found in target/"
            }

            def artifactPath = files[0].path

            echo "📦 Uploading Artifact: ${artifactPath}"
            echo "GroupId: ${pom.groupId}, ArtifactId: ${pom.artifactId}, Version: ${BUILD_NUMBER}"

            nexusArtifactUploader(
                nexusVersion: NEXUS_VERSION,
                protocol: NEXUS_PROTOCOL,
                nexusUrl: NEXUS_URL,
                groupId: pom.groupId,
                version: "${BUILD_NUMBER}",
                repository: NEXUS_REPOSITORY,
                credentialsId: NEXUS_CREDENTIAL_ID,
                artifacts: [
                    [
                        artifactId: pom.artifactId,
                        classifier: '',
                        file: artifactPath,
                        type: pom.packaging
                    ],
                    [
                        artifactId: pom.artifactId,
                        classifier: '',
                        file: 'pom.xml',
                        type: 'pom'
                    ]
                ]
            )
        }
    }

    // Optional: Notification stage
    stage("Result") {
        if (currentBuild.currentResult == 'SUCCESS') {
            echo '✅ Build & Publish succeeded!'
        } else {
            echo '❌ Pipeline failed. Please check logs.'
        }
    }
}
