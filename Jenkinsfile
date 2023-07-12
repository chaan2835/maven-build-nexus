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
         nexusUrl: '43.204.112.58:8081',
         nexusVersion: 'nexus3',
         protocol: 'http',
         repository: 'fav-places',
         version: "${version}"
        }
      }
    }
    stage ("s3-upload"){
      try{
        withCredentials([<object of type com.cloudbees.jenkins.plugins.awscredentials.AmazonWebServicesCredentialsBinding>]) {
            sh "aws s3 ls"
            sh "aws s3 mb s3://jenkins-s3-uploader"
            sh "aws s3 cp **/target/*.war s3://jenkins-s3-uploader"
        }
      }
      catch (err){
        sh "echo error sending artifact to s3 bucket"
      }
    }
  }
}
