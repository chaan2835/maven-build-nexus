pipeline{
  agent any
  tools {
    maven 'maven_3.6.3'
  }

  options{
    timestamps()
    // ansiColor('xterm')
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
    stage("Reading pom contents"){
      steps{
        script{
          def pom = readMavenPom file: 'pom.xml'
          def artifactId = pom.artifactId
          echo "ArtifactId:${artifactId}"
        }
      }
    }

   stage("Uploading Artifact") {
    steps {
        nexusArtifactUploader artifacts:
        [
          [ artifactId: 'favourite-places',
            classifier: '',
            file: 'target/favourite-places-1-SNAPSHOT.war',
            type: 'war'
          ]
        ],
         credentialsId: 'nexus-creds',
         groupId: 'icic',
         nexusUrl: '43.204.112.58:8081',
         nexusVersion: 'nexus3',
         protocol: 'http',
         repository: 'fav-places',
         version: '1-${BUILD_NUMBER}-SNAPSHOT'
      }
    }
  }
}
