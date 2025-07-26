pipeline {
    agent any 
    tools {
        maven "MVN_HOME" // This should match the Maven name in Jenkins global tool config
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "3.85.5.89:8081"
        NEXUS_REPOSITORY = "spring3"
        NEXUS_CREDENTIAL_ID = "Nexus_server" // Add this in Jenkins Credentials
    }
    stages {
        stage("Clone Code") {
            steps {
                git 'https://github.com/betawins/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }
        stage("Maven Build") {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }
        stage("Publish to Nexus") {
            steps {
                script {
                    def pom = readMavenPom file: 'pom.xml'
                    def filesByGlob = findFiles(glob: "target/*.${pom.packaging}")
                    if (filesByGlob.length == 0) {
                        error "No artifact found to upload"
                    }
                    def artifactPath = filesByGlob[0].path
                    echo "*** Uploading: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version: ${BUILD_NUMBER}"

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
        }
    }
}
