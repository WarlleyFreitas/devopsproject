def username = 'Warlley & Alessandro'
env.CC = 'clang'

node {
   
   stage('Build') {
      env.DEBUG_FLAGS = '-g'
      echo 'Building..'
      echo "Hello Mr. ${username}"
      echo "Running ${env.JOB_NAME} (${env.BUILD_ID}) at ${env.JENKINS_URL}"  
      deleteDir()
      checkout scm
      sh "echo 'res' > result"
      stash includes: '**/result', name: 'app'
         if (env.BUILD_ID.toInteger() % 2 == 0) {
            echo 'Execucao PAR'
         } else {
            echo 'Execucao IMPAR'
         } 

      sh 'cat README.md'
   /*sh 'printenv'
          def ambiente = input id: 'test', message: 'Please Provide Parameters', ok: 'Next',
          parameters: [
              choice(name: 'ENVIRONMENT',
                  choices: ['dev','qa'].join('\n'),
                  description: 'Please select the Environment'),
              string(name: 'EXIT',
                  defaultValue: '0',
                  description: 'Please enter the exit code.')
           ]
   exitCode = ambiente['EXIT']
   echo "${ambiente}"*/
    
   }
   
   stage('Test') {
      node() {
         echo 'Testing..'
         //git url: 'https://github.com/wvffreitas/devopsproject.git'
         def mvnHome = tool 'maven'
         sh "${mvnHome}/bin/mvn -B -D maven.test.failure.ignore verify"
         archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
         withAWS(credentials: 'aws-wfreitas') {
         env.AWS_DEFAULT_REGION = 'sa-east-1'
         s3Upload (bucket:"devops-school-wfreitas",
         path:’devops/',
         includePathPattern: '**/*',
         excludePathPattern:'**/*.svg')
        
         parallel FrontendTests: { 
            echo 'Testing Frontend..' 
         },
         BackendTests: { 
            echo 'Testing Backend..' 
         }
     }
      node() {
          echo 'Testing...'
      }
   }
   
   stage('Deploy') {
      node() {
         echo 'Deploying....'
         
         unstash 'app'
         sh 'cat result'
         archiveArtifacts artifacts: '**/result', fingerprint: true
         deleteDir()
     }
      
   }
}
