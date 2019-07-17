import groovy.json.*

node () {
   def mvnHome
    
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      // git 'git@github.com:CMYanko/struts2-showcase-demo.git'
      checkout scm
      
      
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'M3'
      
      // sh 'git rev-parse HEAD > commit'
      // commitId = readFile('commit').trim()
      // sh "echo my commitid ${commitId}"

   }
   stage('Build the application') {
      // Run the maven build
      sh "'${mvnHome}/bin/mvn' clean install"
      
      // Nexus Policy evaluation
      def policyEvaluationResult = nexusPolicyEvaluation failBuildOnNetworkError: false, iqApplication: manualApplication('test'), iqStage: 'build', jobCredentialsId: ''
      
      
      //sh "echo current build status ${currentBuild.result}"
      /*
      if (currentBuild.result == 'FAILURE') {
        postGitHub(commitId, 'failure', 'build', 'Build failed')
        return
      } else {
        postGitHub(commitId, 'success', 'build', 'Build succeeded')
      } */
      
   }

   stage('Build the Docker Image'){
    // Scan application with Release policies applied
    def policyEvaluationResult = nexusPolicyEvaluation failBuildOnNetworkError: false, iqApplication: manualApplication('test'), iqStage: 'stage-release', jobCredentialsId: ''
    
    // Build the image
    sh 'cd webgoat-server && docker build --group-add docker -t jessewebgoat:latest .'
   }

   stage('Deploy Docker Image'){
    sh 'docker-compose up -d'
   }

   stage('Done testing?'){
    input 'Done?'
   }
   
   stage('Kill running WebGoat containers'){
    sh 'docker ps -a | awk \'{ print $1,$2 }\' | grep jessewebgoat | awk \'{print $1 }\' | xargs -I {} docker kill {}'
    sh 'docker ps -a | awk \'{ print $1,$2 }\' | grep webwolf | awk \'{print $1 }\' | xargs -I {} docker kill {}'
   }
   
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
