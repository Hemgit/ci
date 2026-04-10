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
        sh 'whoami'
sh 'echo $HOME'
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
              
            }
        }
}


    stage('artifact upload') {
     steps{
      sh 'mvn deploy -DskipTests'
     }
      post{
     success {
       mail bcc: '', body: 'nexus upload success', cc: '', from: '', replyTo: '', subject: 'artifact uploaded', to: '5hemanthunplugged@gmail.com'
     }
      }
    }

    stage('imagebuild'){
      steps{
        sh 'pwd'
        sh 'mv *.war student.war'
        sh 'docker build -t testimage:v1 .' 
      }
    }
    
  }
  }

