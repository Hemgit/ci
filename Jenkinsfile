pipeline{
agent{
  label 'buildserver'
}
  tools{
    jdk 'jdk8'
    maven 'm3'
  }
  stages{
  stage('fetchcode')
    {
      steps{
        git credentialsId: 'github', url: 'https://github.com/Hemgit/Maven-Java-Project.git'
      }
    }
    stage('build'){
    steps{
      sh 'mvn install -DskipTests'
    }
      post{
        success{
       archive 'target/*.war'
        }
        failure{
          mail bcc: '', body: 'build failed', cc: '', from: '', replyTo: '', subject: 'build failed', to: '5hemanthunplugged@gmail.com'
        }
      }
    }

    
    stage('Test'){
     steps{
      sh 'mvn test'
     }
      post{
       success{
         junit 'target/surefire-reports/*.xml'
       }
        failure{
        mail bcc: '', body: 'unit test failed', cc: '', from: '', replyTo: '', subject: 'unit test failed', to: '5hemanthunplugged@gmail.com'
        }
      }
    }
   stage('integration testing'){
     steps{
      sh 'mvn verify -DskipUnitTests'
     }
     post{
        success{
          junit 'target/failsafe-reports/*.xml'
        }
       failure{
        mail bcc: '', body: 'integration test failed', cc: '', from: '', replyTo: '', subject: 'integration test failed', to: '5hemanthunplugged@gmail.com'
       }
     }
   
  }

    

 stage('SonarQube Analysis') {
    tools {
        jdk 'jdk17'
    }
    steps {
        withSonarQubeEnv('sonar') {
            sh 'mvn sonar:sonar -Dsonar.projectKey=Maven-Java-Project'
        }
    }
}

stage('Quality Gate') {
    steps {
        
            timeout(time: 5, unit: 'MINUTES') {

                waitForQualityGate abortPipeline: false
              /*
               script {
                        // Get Quality Gate result
                        def qg = waitForQualityGate()  // returns a map with status
                        echo "SonarQube Quality Gate Status: ${qg.status}"

                        // Archive Quality Gate result as JSON
                        writeFile file: 'quality-gate.json', text: groovy.json.JsonOutput.toJson(qg)
                        archiveArtifacts artifacts: 'quality-gate.json', allowEmptyArchive: true

                        // Fail pipeline if Quality Gate failed
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to Quality Gate failure: ${qg.status}"
                        }
             
                }
                */
            }
        }
}


    stage('artifact upload') {
     steps{
      sh 'mvn deploy -DskipTests --settings /home/devops/.m2/settings.xml'
     }
      post{
     success {
       mail bcc: '', body: 'nexus upload success', cc: '', from: '', replyTo: '', subject: 'artifact uploaded', to: '5hemanthunplugged@gmail.com'
     }
      }
    }
    
  }
  }

