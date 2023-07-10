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
          def groupId = pom.groupId
          def version = pom.version
          def packaging = pom.packaging
          echo "#####################################################################"
          echo "ArtifactId:${artifactId}"
          echo "Groupid:${groupId}"
          echo "version:${version}"
          echo "packaging:${packaging}"
          echo "######################################################################"
        }
      }
    }

   stage("Uploading Artifact") {
    steps {
        nexusArtifactUploader artifacts:
        [
          [ artifactId: '${artifactId}',
            classifier: '',
            file: 'target/*.war',
            type: '${packaging}'
          ]
        ],
         credentialsId: 'nexus-creds',
         groupId: '${groupId}',
         nexusUrl: '43.204.112.58:8081',
         nexusVersion: 'nexus3',
         protocol: 'http',
         repository: 'fav-places',
         version: '1-${BUILD_NUMBER}-SNAPSHOT'
      }
    }
  }
}
