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
      sh 'mvn verify'
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

  
  }
  
}
