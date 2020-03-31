 node{
         Stage('SCM checkout'){
                  git 'https://github.com/Raouagarati101/maven-exemple'
         }
         Stage('compile stage'){    
         def mvnHome = tool name: 'apache-maven-3.6.3', type: 'maven'
                  sh "${mvnHome}/bin/mvn package"
         }
}
