import groovy.json.*

node () {
   def mvnHome, commitId
    
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      // git 'git@github.com:CMYanko/struts2-showcase-demo.git'
      //checkout scm
      
      
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      // mvnHome = tool 'M3'
      
      // sh 'git rev-parse HEAD > commit'
      // commitId = readFile('commit').trim()
      // sh "echo my commitid ${commitId}"

   }
   stage('Build') {
      // Run the maven build
      checkout scm
      
      sh "mvn clean install"

      //sh "echo current build status ${currentBuild.result}"
      /*
      if (currentBuild.result == 'FAILURE') {
        postGitHub(commitId, 'failure', 'build', 'Build failed')
        return
      } else {
        postGitHub(commitId, 'success', 'build', 'Build succeeded')
      } */
      
   }

   
   stage('Lifecycle Evaluation'){
    // postGitHub commitId, 'pending', 'analysis', 'Nexus Lifecycle Analysis is running'

      def policyEvaluationResult = nexusPolicyEvaluation failBuildOnNetworkError: false, iqApplication: manualApplication('test'), iqStage: 'stage-release', jobCredentialsId: ''
    /*  if (currentBuild.result == 'FAILURE'){
        postGitHub commitId, 'failure', 'analysis', 'Nexus Lifecycle Analysis failed',"${policyEvaluationResult.applicationCompositionReportUrl}"
        return
      } else {
        postGitHub commitId, 'success', 'analysis', 'Nexus Lifecycle Analysis succeeded',"${policyEvaluationResult.applicationCompositionReportUrl}"
      } */
   }

   
   stage('Build Docker Image'){
    sh 'pwd'
    sh 'ls -l'
    sh 'cd webgoat-server && docker build -t jessewebgoat:latest .'
   }
   
   stage('Deploy Docker Image'){
    sh 'cd .. && docker-compose up -d'
   }
   
   stage('Done testing?'){
    input 'Done?'
   }
   
   //stage('Kill running Struts container'){
   // sh 'docker ps -a | awk \'{ print $1,$2 }\' | grep struts2-rce | awk \'{print $1 }\' | xargs -I {} docker kill {}'
    //sh 'mvn -v'
   //}
   
}

def postGitHub(commitId, state, context, description, target_url="http://localhost:8080") {
         def payload = JsonOutput.toJson(
           state: state,
           context: context,
           description: description,
           target_url: target_url
          )
        sh "curl -H \"Authorization: token ${gitHubApiToken}\" --request POST --data '${payload}' https://api.github.com/repos/${project}/statuses/${commitId}"
}
