import groovy.json.*

node () {
   def mvnHome
    
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/JesseHouldsworth/WebGoat'
      //checkout scm
      
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      //mvnHome = tool 'M3'
   }
   stage('Build the application') {
      // Run the maven build
      //sh "'${mvnHome}/bin/mvn' clean install"
      sh 'mvn -v'
      sh 'mvn clean install'
   }
   stage('Test the application') {
      // Run the Sonatype scan
      def policyEvaluationResult = nexusPolicyEvaluation failBuildOnNetworkError: false, iqApplication: manualApplication('webgoat8'), iqStage: 'build', jobCredentialsId: ''
   } 
   stage('Deploy the application') {
      // deploy script.....
   }
}
