node {
   environment {
      NEXUS_VERSION  = "nexus3"
      NEXUS_PROTOCOL = "http"
      NEXUS_URL = "localhost:8081"
      NEXUS_REPOSITORY = "nexus-test"
    //  NEXUS_CREDENTAL_ID = "nexus_credentials"
   }
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/Raouagarati101/maven-exemple.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'apache-maven-3.6.3'
   }
   Stages {
   stage('Build') {
      // Run the maven build
      withEnv(["MVN_HOME=$mvnHome"]) {
         if (isUnix()) {
            sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
         } else {
            bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
         }
      }
   }
   stage('publish to nexus') {
      steps {
         script {
            pom = readMavenPom file: "pom.xml";
            filesByGlob = findFiles(glob : "target/*.${pom.packaging}");
            echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
            artifactPath = filesByGlob[0].path;
            artifactExists = fileExists artifactPath;
            if (artifactExists) {
               echo "*** File : ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version: ${pom.version}"
               nexusArtifactUploader(
                  nexusVersion: NEXUS_VERSION,
                  protocol: NEXUS_PROTOCOL,
                  nexusUrl: NEXUS_URL,
                  groupID: pom.groupId,
                  version: '${BUILD_NUMBER}',
                  repository: NEXUS_REPOSITORY,
                  credentialsId: NEXUS_CREDENTIAL_ID,
                  artifacts: [
                     [artifactId: pom.artifactId,
                      classifier: '',
                      file: "pom.xml",
                      type: "pom",
                      ]
                     ]
                  );
            } else { error "*** File: ${artifactPath}, could not be found";
                   }
         }
         }
      }
   }              
}
