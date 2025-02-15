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
         timeout(time: 2, unit: 'MINUTES') {
           withSonarQubeEnv('SonarQube_server') {
             sh "mvn clean verify sonar:sonar \
               -Dsonar.projectKey=demo-project \
               -Dsonar.projectName='demo-project' \
               -Dsonar.host.url=http://10.0.254.42:9000 \
               -Dsonar.token=sqp_8376f4b2c23b4fedfc27dd92970362f4eba063db "
              
           }
           script {
             def qualityGateResult = waitForQualityGate()
             if (qualityGateResult.status != 'OK') {
               // Quality gate failed, but we're not aborting the pipeline
               currentBuild.result = 'SUCCESS'  // Mark the build as successful
               echo "Warning: SonarQube Quality Gate failed with status: ${qualityGateResult.status}"
             }
           }
         }
       }
     }
   }
}
