node {
   def mvnHome
   def artifactname = "globex-web.war"
   def repoName = "demorepo"
   def pkgName = "globex-package"
   stage('Setup') { // for display purposes
      snDevOpsStep(enabled:true)
      // Get some code from a GitHub repository
      git branch: 'master', credentialsId: 'sn-devops', url: 'https://github.com/snow-devops/CorpSite1'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.          
      mvnHome = tool 'maven'
   }
   stage('Build') {
      // Run the maven build
      snDevOpsStep(enabled:true)
      withEnv(["MVN_HOME=$mvnHome"]) {
         if (isUnix()) {
            sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
         } else {
            bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)
         }
      }
   }
   stage('Results') {
      snDevOpsStep(enabled:true)
      //includeBuildInfo=true
     
      archiveArtifacts 'target/*.war'
      snDevOpsArtifact(artifactsPayload: """{"artifacts": [{"name": "${artifactname}", "version": "1.${env.BUILD_NUMBER}","semanticVersion": "1.${env.BUILD_NUMBER}.0","repositoryName": "${repoName}"}],"branchName":"master"}""")
   }
   stage('UAT Test') {
       snDevOpsStep(enabled:true)
       junit 'target/surefire-reports/*.xml'
       

   }
      stage('Deploy') {
      snDevOpsStep(enabled:true)
      snDevOpsPackage(name: "${pkgName}-${env.BUILD_NUMBER}", artifactsPayload: """{"artifacts":[{"name": "${artifactname}", "version": "1.${env.BUILD_NUMBER}", "repositoryName": "${repoName}"}], "branchName":"master"}""")
      snDevOpsChange()
      sh "cp -f target/globex-web.war /opt/tomcat/webapps/globex-web.war"
   }
   }
