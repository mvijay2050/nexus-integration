pipeline {
    agent {
        label "master"
    }
    tools {
        maven "MAVEN_3.6.3"
    }
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "localhost:6061"
        NEXUS_REPOSITORY = "maven-snapshots" // "maven-nexus-repo" //maven-snapshots
        NEXUS_CREDENTIAL_ID = "localNexus" //"nexus-user-credentials" //localNexus
    }
    stages {
        stage("Clone Code") {
            steps {
                script {
                    git 'https://github.com/mvijay2050/nexus-integration.git';
                }
            }
        }
        stage("Maven Build") {
            steps {
                script {
                    sh "mvn package -DskipTests=true"
                }
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {

                    /* 
                    nexusArtifactUploader 
                        credentialsId: 'localNexus', 
                        groupId: 'group.id', 
                        nexusUrl: 'localhost:6061', 
                        nexusVersion: 'nexus3', 
                        protocol: 'http', 
                        repository: 'test-repo', 
                        version: 'version'
                    */
                    echo "--> 5.1"
                    pom = readMavenPom file: "pom.xml";
                    echo "--> 5.2:::: ${pom}"
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "--> 5.3-- ${filesByGlob}"
                    
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    echo "--> 5.4"
                    artifactExists = fileExists artifactPath;
                    echo "--> 5.5"
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}
