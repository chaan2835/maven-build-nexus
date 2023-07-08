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

  //  stage("Publish to Nexus Repository Manager") {
  //   steps {
  //       nexusArtifactUploader artifacts: [
  //         [artifactId: 'favourite-places',
  //           classifier: '',
  //           file: 'target/favourite-places-1.0.1.war',
  //           type: 'war']
  //         ],
  //         credentialsId: 'nexus',
  //         groupId: 'icic',
  //         nexusUrl: '172.31.33.66',
  //         nexusVersion: 'nexus3',
  //         protocol: 'http',
  //         repository: 'fav-places',
  //         version: '1.0.1'
  //     }
  //   }
  }
}
