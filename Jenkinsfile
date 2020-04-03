node {
   def mvnHome
   stage('Preparation') {
      git 'https://github.com/Raouagarati101/maven-exemple.git'
      mvnHome = tool 'apache-maven-3.6.3'
   }
   stage('Build') {
      if (isUnix()) {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
      } else {
         bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
      }
   }
   stage('Results') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archive 'target/*.jar'
   }
   stage('Publish') {
     nexusPublisher nexusInstanceId: 'admin', nexusRepositoryId: 'nexus-test', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'war/target/jenkins.war']], mavenCoordinate: [artifactId: 'jeunit', groupId: 'junit', packaging: 'war', version: '4.12']]]
   }
}
