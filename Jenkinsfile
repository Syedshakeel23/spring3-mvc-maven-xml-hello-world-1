pipeline {
    agent any

    tools {
        maven "MVN_HOME" // This must match the Maven installation name in Jenkins → Global Tool Configuration
    }

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"

        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "204.236.252.196:8081"
        NEXUS_REPOSITORY = "spring3"
        NEXUS_CREDENTIAL_ID = "Nexus_server" // You must define this ID in Jenkins Credentials
    }

    stages {
        stage("Clone Code") {
            steps {
                git url: 'https://github.com/Syedshakeel23/spring3-mvc-maven-xml-hello-world-1.git'
            }
        }

        stage("Build WAR") {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'
            }
        }

        stage("Publish to Nexus") {
            steps {
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
                        artifactId: pom.artifactId,
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

    post {
        success {
            echo '✅ Build & Publish succeeded!'
        }
        failure {
            echo '❌ Pipeline failed. Please check logs.'
        }
    }
}
