pipeline {
    agent any 
    tools { 
        maven 'Maven' 
      jdk 'java'
    }
stages { 
     
 stage('Preparation') { 
     steps {
// for display purposes

      // Get some code from a GitHub repository

      git 'https://github.com/raknas999/hello-world-servlet.git'

      // Get the Maven tool.
     
 // ** NOTE: This 'M3' Maven tool must be configured
 
     // **       in the global configuration.   
     }
   }

   stage('Build') {
       steps {
       // Run the maven build

      //if (isUnix()) {
         sh 'mvn -Dmaven.test.failure.ignore=true install'
      //} 
      //else {
      //   bat(/"${mvnHome}\bin\mvn" -Dmaven.test.failure.ignore clean package/)
       }
//}
   }
 
  stage('Results') {
      steps {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.war'
      }
 }
 stage('Sonarqube') {
    environment {
        scannerHome = tool 'sonarqube'
    }
    steps {
        withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
  //      timeout(time: 10, unit: 'MINUTES') {
 //           waitForQualityGate abortPipeline: true
  //      }
    }
     
          freeStyleJob('NexusArtifactUploaderJob') {
        steps {
          nexusArtifactUploader {
            nexusVersion('nexus3')
            protocol('http')
            nexusUrl('13.58.37.70:8081')
            groupId('com.geekcap.vmturbo')
            version('1.0')
            repository('NexusArtifactUploader')
            credentialsId('44620c50-1589-4617-a677-7563985e46e1')
            artifact {
                artifactId('hello-world-servlet-example')
                type('war')
                classifier('debug')
                file('helloworld.war')
            }
                      }
        }
    }
nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: '13.58.37.70:8081',
        groupId: 'com.geekcap.vmturbo',
        version: 1.0,
        repository: 'hello-world-servlet',
        credentialsId: 'CredentialsId',
        artifacts: [
            [artifactId: hello-world-servlet-example,
             classifier: '',
             file: 'target/helloworld.war',
             type: 'war']
        ]
     )
   
     stage('Artifact upload') {
      steps {
     //nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
     //nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'maven-releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/helloworld.war']], mavenCoordinate: [artifactId: 'hello-world-servlet-example', groupId: 'com.geekcap.vmturbo', packaging: 'war', version: '$BUILD_NUMBER']]]
       nexusArtifactUploader artifacts: [[artifactId: 'hello-world-servlet-example', classifier: '', file: 'target/helloworld.war', type: 'war']], credentialsId: 'nexus-cred', groupId: 'com.geekcap.vmturbo', nexusUrl: '13.58.37.70:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'hello-world-servlet', version: '${BUILD_NUMBER}'   
      }
 }
    
     stage('Update Artifact Version') {
      steps {
        sh label: '', script: '''sed -i s/artifactversion/$BUILD_NUMBER/ deploy.yml'''
      }
 }
     stage('Deploy War') {
      steps {
        sh label: '', script: 'ansible-playbook deploy.yml'
      }
 }
}
post {
        success {
            mail to:"raknas000@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Build success"
        }
        failure {
            mail to:"raknas000@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Build failed"
        }
    }       
}
