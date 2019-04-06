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
   }
   
   stage('Test') {
      
         echo 'Testing..'
         //git url: 'https://github.com/wvffreitas/devopsproject.git'
         def mvnHome = tool 'maven'
         sh "${mvnHome}/bin/mvn -B -D maven.test.failure.ignore verify"
         archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
         withAWS(credentials: 'aws-freitas') {
         env.AWS_DEFAULT_REGION = 'sa-east-1'
         s3Upload (bucket:"devops-school-wfreitas",
         path:'devops/',
         includePathPattern: '**/*',
         excludePathPattern:'**/*.svg')
       
   }
   
   stage('Deploy') {
      node() {
         echo 'Deploying....'
         deleteDir()
         unstash 'app'
         sh 'cat result'
         archiveArtifacts artifacts: '**/result', fingerprint: true
         
     }
      
   }
   }
}
