node {
    def mvnHome = tool 'MVN_HOME'

    env.JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
    env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
    
    env.NEXUS_VERSION = 'nexus3'
    env.NEXUS_PROTOCOL = 'http'
    env.NEXUS_URL = '204.236.252.196:8081'
    env.NEXUS_REPOSITORY = 'spring3'
    env.NEXUS_CREDENTIAL_ID = 'Nexus_server'

    stage('Clone Code') {
        git url: 'https://github.com/Syedshakeel23/spring3-mvc-maven-xml-hello-world-1.git'
    }

    stage('Build WAR') {
        sh "${mvnHome}/bin/mvn -Dmaven.test.failure.ignore=true clean install"
    }

    stage('Upload to Nexus') {
        script {
            def pom = readMavenPom file: 'pom.xml'
            def files = findFiles(glob: "target/*.${pom.packaging}")

            if (files.length == 0) {
                error "❌ No ${pom.packaging} file found in target/"
            }

            def artifactPath = files[0].path

            echo "📦 Uploading Artifact: ${artifactPath}"
            echo "GroupId: ${pom.groupId}, ArtifactId: ${pom.artifactId}, Version: ${BUILD_NUMBER}"

            nexusArtifactUploader(
                nexusVersion: env.NEXUS_VERSION,
                protocol: env.NEXUS_PROTOCOL,
                nexusUrl: env.NEXUS_URL,
                groupId: pom.groupId,
                artifactId: pom.artifactId,
                version: "${BUILD_NUMBER}",
                repository: env.NEXUS_REPOSITORY,
                credentialsId: env.NEXUS_CREDENTIAL_ID,
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

    stage('Done') {
        echo '✅ Build & Upload completed!'
    }
}
