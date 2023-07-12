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

   stage("Uploading Artifact") {
    steps {
      script{
        def pom = readMavenPom file: 'pom.xml'
          def artifactId = pom.artifactId
          def groupId = pom.groupId
          def version = pom.version
          def packaging = pom.packaging
          echo "#####################################################################"
          echo "ArtifactId: ${artifactId}"
          echo "Groupid: ${groupId}"
          echo "version: ${version}"
          echo "packaging: ${packaging}"
          echo "######################################################################"
            nexusArtifactUploader artifacts:
        [
          [ artifactId: "${artifactId}",
            classifier: '',
            file: "target/${artifactId}-${version}.war",
            type: "${packaging}"
          ]
        ],
         credentialsId: 'nexus-creds',
         groupId: "${groupId}",
         nexusUrl: '13.235.70.182:8081',
         nexusVersion: 'nexus3',
         protocol: 'http',
         repository: 'fav-places',
         version: "${version}"
        }
      }
    }
    stage ("s3-upload"){
      steps{
        s3Upload consoleLogLevel: 'INFO',
                 dontSetBuildResultOnFailure: false,
                 dontWaitForConcurrentBuildCompletion: false,
                 entries: [
                    [bucket: 'jenkins-s3-uploader',
                     excludedFile: '',
                     flatten: false,
                     gzipFiles: false,
                     keepForever: false,
                     managedArtifacts: false,
                     noUploadOnFailure: false,
                     selectedRegion: 'ap-south-1',
                     showDirectlyInBrowser: false,
                     sourceFile: '**/target/*.war',
                     storageClass: 'STANDARD',
                     uploadFromSlave: false,
                     useServerSideEncryption: false]
                    ], pluginFailureResultConstraint: 'FAILURE',
                       profileName: 'jenkins-s3-uploader',
                       userMetadata: []
      }
    }
  }
}
