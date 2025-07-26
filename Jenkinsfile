node {
    // Set environment manually
    env.JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
    env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"

    def nexusVersion = "nexus3"
    def nexusProtocol = "http"
    def nexusUrl = "204.236.252.196:8081"
    def nexusRepository = "spring3"
    def nexusCredentialId = "Nexus_server"

    def groupId = "com.ncodeit"
    def artifactId = "ncodeit-hello-world"
    def packaging = "war"
    def version = "${env.BUILD_NUMBER}"
    def artifactFile = "target/${artifactId}-${version}.${packaging}"

    stage('Clone Code') {
        git url: 'https://github.com/Syedshakeel23/spring3-mvc-maven-xml-hello-world-1.git'
    }

    stage('Build WAR') {
        sh 'mvn -Dmaven.test.failure.ignore=true clean install'
    }

    stage('Publish to Nexus') {
        echo "📦 Uploading Artifact: ${artifactFile}"
        echo "GroupId: ${groupId}, ArtifactId: ${artifactId}, Version: ${version}"

        nexusArtifactUploader(
            nexusVersion: nexusVersion,
            protocol: nexusProtocol,
            nexusUrl: nexusUrl,
            groupId: groupId,
            artifactId: artifactId,
            version: version,
            repository: nexusRepository,
            credentialsId: nexusCredentialId,
            artifacts: [
                [
                    artifactId: artifactId,
                    classifier: '',
                    file: artifactFile,
                    type: packaging
                ],
                [
                    artifactId: artifactId,
                    classifier: '',
                    file: 'pom.xml',
                    type: 'pom'
                ]
            ]
        )
    }

    echo '✅ Build & Publish completed!'
}
