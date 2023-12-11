pipeline{
  agent any
  tools {
    maven "MAVEN3"
    jdk "OracleJDK8"
  }
  
  environment {
    SNAP_REPO = 'vprofile-snapshot'
    NEXUS_USER = 'admin'
    NEXUS_PASS = 'Admin123'
    RELEASE_REPO = 'vprofile-release'
    CENTRAL_REPO = 'vpro-maven-central'
    NEXUSIP = '172.31.91.210'
    NEXUSPORT = '8081'
    NEXUS_GRP_REPO = 'vpro-maven-group'
    NEXUS_LOGIN = 'nexuslogin'
    SONARQUBESERVER = 'sonaqubeserver'
    SONARQUBESCANNER = 'sonarqubescanner'
  }
  stages {
    stage('Build'){
      steps {
        sh 'mvn -s settings.xml -DskipTests install'
      }
      post {
        success {
          echo "Now Archiving"
          archiveArtifacts artifacts: '**/*.war'
        }
      }
    }
    stage('Test'){
      steps {
        sh "mvn -s settings.xml test"
      }
    }
    stage('Checkstyle Analysis'){
      steps {
        sh "mvn -s settings.xml checkstyle:checkstyle"
      }
    }
    stage('Sonar Analysis') {
      environment {
        scannerHome = tool "${SONARQUBESCANNER}"
      }
      steps {
        withSonarQubeEnv("${SONARQUBESERVER}") {
          sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
          -Dsonar.projectName=vprofile \
          -Dsonar.projectVersion=1.0 \
          -Dsonar.sources=src/ \
          -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
          -Dsonar.junit.reportsPath=target/surefire-reports/ \
          -Dsonar.jacoco.reportsPath=target/jacoco.exec \
          -Dsonar.java.checkstyle.reportsPaths=target/checkstyle-result.xml'''
        }
      }
    }
  }
}