node {
   environnement {
        // Cela peut être nexus3 ou nexus2
        NEXUS_VERSION  =  "nexus3"
        // Cela peut être http ou https
        NEXUS_PROTOCOL  =  "http"
        // Où fonctionne votre Nexus
        NEXUS_URL  =  "172.17.0.3:8081"
        // Référentiel où nous téléchargerons l'artefact
        NEXUS_REPOSITORY  =  "exemple-référentiel"
        // Identifiant Jenkins pour s'authentifier auprès de Nexus OSS
        //NEXUS_CREDENTIAL_ID  =  "nexus-credentials"
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
 stade ( "publier sur nexus" ) {
            étapes {
                script {
                    // Lire le fichier xml POM en utilisant l'étape 'readMavenPom', cette étape 'readMavenPom' est incluse dans: https://plugins.jenkins.io/pipeline-utility-steps
                    pom  =  fichier readMavenPom  :  "pom.xml" ;
                    // Trouver un artefact construit dans le dossier cible
                    filesByGlob  =  findFiles ( glob:  "target /*.$ { pom . packaging }" );
                    // Imprimer des informations sur l'artefact trouvé
                    echo  "$ { filesByGlob [ 0 ]. nom } $ { filesByGlob [ 0 ]. chemin } $ { filesByGlob [ 0 ]. répertoire } $ { filesByGlob [ 0 ]. longueur } $ { filesByGlob [ 0 ]. lastModified }"
                    // Extraire le chemin du fichier trouvé
                    artifactPath  =  filesByGlob [ 0 ]. chemin ;
                    // Assigner à une réponse booléenne vérifiant si le nom de l'artefact existe
                    artifactExists  =  fileExists  artifactPath ;
                    if ( artifactExists ) {
                        echo  "*** Fichier: $ { artifactPath }, groupe: $ { pom . groupId }, emballage: $ { pom . packaging }, version $ { pom . version }" ;
                        nexusArtifactUploader (
                            nexusVersion:  NEXUS_VERSION ,
                            protocole:  NEXUS_PROTOCOL ,
                            nexusUrl:  NEXUS_URL ,
                            groupId:  pom . groupId ,
                            version:  pom . version ,
                            référentiel:  NEXUS_REPOSITORY ,
                            //credentialsId:  NEXUS_CREDENTIAL_ID ,
                            artefacts: [
                                // Artefact généré tel que les fichiers .jar, .ear et .war.
                                [ artifactId:  pom . artifactId ,
                                classificateur:  '' ,
                                fichier:  artifactPath ,
                                type:  pom . emballage ],
                                // Permet de télécharger le fichier pom.xml pour plus d'informations sur les dépendances transitives
                                [ artifactId:  pom . artifactId ,
                                classificateur:  '' ,
                                fichier:  "pom.xml" ,
                                type:  "pom" ]
                            ]
                        );
                    } else {
                        erreur  "*** Fichier: $ { artifactPath }, introuvable" ;
                    }
                }
            }
        }
    }
}
