pipeline {
agent any&lt;/code&gt;
 booleanParam(name: 'executeSonarQube',defaultValue:false, description:'')
 booleanParam(name: 'Deploy',defaultValue:false, description:'')
 
tools{
maven 'maven'
jdk 'jdk8'
}
 
stages {
stage ("initialize") {
steps {
sh '''
echo "PATH = ${PATH}"
echo "M2_HOME = ${M2_HOME}"
'''
}
}
  stage ('Build project') {
steps {
dir("project_templates/java_project_template"){
sh 'mvn clean verify
 
}
}
}
 
 stage('Approval'){
  steps{
   input('Do you want to proceede?')
  }
 }
  
  stage ('SonarQube Analysis'){
steps{
dir("project_templates/java_project_template"){
withSonarQubeEnv('sonarqubescanner') {
sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
}
}
}
}
  
  stage ('Artifactory Deploy'){
when {
branch "master"
}
steps{
dir("project_templates/java_project_template"){
script {
def server = Artifactory.server('Artifactory Version 4.15.0')
def rtMaven = Artifactory.newMavenBuild()
rtMaven.resolver server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'
rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
rtMaven.tool = 'maven 3'
def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'install'
server.publishBuildInfo buildInfo
}
}
}
}
  
  post {
success {
slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
 
}
failure {
slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
 
}
}
  
  
}
}
