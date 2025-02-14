pipeline {
  agent any
  tools {
    maven "maven3.9.9"
  }

  stages {
    stage('Build Artifact') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }

    stage('Test Maven - JUnit') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage('SonarQube Analysis - SAST') {
      steps {
        withSonarQubeEnv('SonarQube_server') {
          sh "mvn clean verify sonar:sonar \
            -Dsonar.projectKey=demo-project \
            -Dsonar.projectName='demo-project' \
            -Dsonar.host.url=http://10.0.254.42:9000 \
            -Dsonar.token=sqp_8376f4b2c23b4fedfc27dd92970362f4eba063db \
            -Dsonar.java.binaries=target/classes \
            -Dsonar.profile=Acute-Java-Quality-Profiles"
        }
      }
    }
  }
}
