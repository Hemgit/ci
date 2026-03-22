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
      sh 'mvn verify -DskipTests'
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

    stage('sonaranalysis')
    {
       tools{
      jdk 'jdk17'
       }
     steps{
      
      withSonarQubeEnv('sonar') {
        sh 'mvn sonar:sonar -Dsonar.projectKey=Maven-Java-Project'
    // some block
}
      
         
       }

       post{
      success {
       sh 'echo sonar success'
      }
        failure{
            mail bcc: '', body: 'sonar code analysis failed', cc: '', from: '', replyTo: '', subject: 'sonar code analysis  failed', to: '5hemanthunplugged@gmail.com'
        }
     }
    }

   stage('qualitygate'){
     steps{

 timeout(time: 10, unit: 'minutes')  {
   waitForQualityGate abortPipeline: false, credentialsId: 'sonarkey'
}
     
     }
   }
    
  }
  
}
