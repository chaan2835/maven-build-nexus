pipeline{
  agent any
  tools {
    maven 'maven_3.6.3'
  }
  options{
    timestamps()
    ansiColor('xterm')
    buildDiscarder(logRotator(numToKeepStr:"8"))
  }
  stages{
    stage("Maven-Build"){
      steps{
          sh "mvn clean package"
      }
      post {
        success {
          ansiColor("blue"){
          echo "############################ Archiving the Artifacts ########################"
          archiveArtifacts artifacts: "**/target/*.war"
          }
        }
      }
    }
    stage("Artifact exists"){
        steps{
          ansiColor("magenta"){
            echo "####################checking the file in workspace##################"
          }
          fileExists '/root/.jenkins/workspace/**/target/*war'
          ansiColor ("green"){
            echo "######################file exists###################"
          }
      }
    }
  }
}
