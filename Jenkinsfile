node {
  stage('Build') {
    echo 'Building..'
    sh "mvn clean"
    deleteDir()
    checkout scm
    sh 'cat README.md'
  }
  stage('Test') {
    echo 'Testing..'
    sh "mvn test"
  }
  stage('Deploy') {
    echo 'Deploying....'
    sh "mvn package"
  }
}
