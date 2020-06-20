def server = Artifactory.server 'Artifactory Version 4.15.0'
def rtMaven = Artifactory.newMavenBuild()
def buildInfo

pipeline{
agent any
tools{
jdk "jdk8 "
maven "maven"
}
stages {
stage('Clone sources'){
steps{
git url: 'https://github.com/Navya-2020/HappyTrip-JAVA.git'
}
}
stage('SonarQube Analysis'){
steps{
withSonarQubeEnv('sonarqubescanner'){
bat 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar'
}
}
}
stage('Artifactory configuration'){
steps{
script{
rtMaven.tool='maven'
rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local, server:server'
rtMaven.resolver releaseRepo: 'libs-release',snapshotRepo: 'libs-snapshot', server:server
rtMaven.deployer.artifactDeployementPatterns.addExclude("pom.xml")
buildInfo.retention maxBuilds: 10, maxDays: 7, deleteBuildArtifacts: true
buildInfo.env.capture = true
}
}
}
}
}
