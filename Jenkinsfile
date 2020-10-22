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
        NEXUS_SNAPSHOT_REPO = "maven-snapshots"
        NEXUS_RELEASE_REPO = "maven-releases"
        NEXUS_CREDENTIAL_ID = "localNexus" //"nexus-user-credentials" //localNexus

        MULE_PACKAGING = "jar"
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
                    sh "mvn clean package -DskipTests=true"
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
                    echo "--> 5.2:::: ${pom}" //com.mycompany:mule-nexus-demo:mule-application:1.0.0-SNAPSHOT
                    echo "POM GroupID--> ${pom.groupId}" //com.mycompany
                    echo "POM Packaging--> ${pom.packaging}" //mule-application
                    echo "POM Version--> ${pom.version}" //1.0.0-SNAPSHOT
                    //filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    filesByGlob = findFiles(glob: "target/*.jar"); //[target/mule-nexus-demo-1.0.0-SNAPSHOT-mule-application.jar]
                    //echo "--> 5.3-- ${filesByGlob}"
                    
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path; //target/mule-nexus-demo-1.0.0-SNAPSHOT-mule-application.jar
                    echo "--> 5.4 Artifact Path :: ${artifactPath}"
                    artifactExists = fileExists artifactPath;
                    echo "--> 5.5 artifactExists ${artifactExists}"

                    isSnapShot = (pom.version).contains("-SNAPSHOT")
                    echo "Is SnapShot --> ${isSnapShot}"
                    echo "00000 ---> ${isSnapShot} ? ${NEXUS_SNAPSHOT_REPO} : ${NEXUS_RELEASE_REPO}"

                    if(artifactExists) {
                        //echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: MULE_PACKAGING, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            //repository: NEXUS_REPOSITORY,
                            repository: ${isSnapShot} ? ${NEXUS_SNAPSHOT_REPO} : ${NEXUS_RELEASE_REPO},
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: MULE_PACKAGING],
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
