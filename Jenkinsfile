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
      echo 'Testing..'
      //git url: 'https://github.com/wvffreitas/devopsproject.git'
      def mvnHome = tool 'maven'
      sh "${mvnHome}/bin/mvn -B -D maven.test.failure.ignore verify"
      archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
        
      parallel FrontendTests: { echo 'Testing Frontend..' },
               BackendTests: { echo 'Testing Backend..' }
   }
   
   stage('Deploy') {
      node() {
         echo 'Deploying....'
         deleteDir()
         unstash 'app'
         sh 'cat result'
         //sh 'zip -r deploy.zip /var/jenkins_home/workspace/pipeline@2'
         publishHTML target: [
                        allowMissing: true,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'target/reports/html',
                        reportFiles: 'index.html',
                        reportName: 'Test Suite HTML Report'
                ]
         archiveArtifacts artifacts: '**/result', fingerprint: true
      }
 }

}
