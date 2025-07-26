pipeline {
    agent any

    tools {
        maven "MVN_HOME"
    }

    environment {
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"

        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "204.236.252.196:8081"
        NEXUS_REPOSITORY = "spring3"
        NEXUS_CREDENTIAL_ID = "Nexus_server"
        
        GROUP_ID = "com.mycompany"
        ARTIFACT_ID = "spring3-mvc-maven-xml-hello-world"
        PACKAGING = "war"
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
                    def files = findFiles(glob: "target/*.${env.PACKAGING}")

                    if (files.length == 0) {
                        error "No ${env.PACKAGING} file found in target/"
                    }

                    def artifactPath = files[0].path

                    echo "📦 Uploading Artifact: ${artifactPath}"
                    echo "GroupId: ${env.GROUP_ID}, ArtifactId: ${env.ARTIFACT_ID}, Version: ${BUILD_NUMBER}"

                    nexusArtifactUploader(
                        nexusVersion: env.NEXUS_VERSION,
                        protocol: env.NEXUS_PROTOCOL,
                        nexusUrl: env.NEXUS_URL,
                        groupId: env.GROUP_ID,
                        artifactId: env.ARTIFACT_ID,
                        version: "${BUILD_NUMBER}",
                        repository: env.NEXUS_REPOSITORY,
                        credentialsId: env.NEXUS_CREDENTIAL_ID,
                        artifacts: [
                            [
                                artifactId: env.ARTIFACT_ID,
                                classifier: '',
                                file: artifactPath,
                                type: env.PACKAGING
                            ],
                            [
                                artifactId: env.ARTIFACT_ID,
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
