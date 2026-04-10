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

    
   stage('integration testing'){
     steps{
      sh 'mvn verify '
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
        sh 'ls -ltr && pwd'
        sh 'mv target/*.war student.war'
        sh 'docker build -t testimage:v1 .' 
        sh 'docker tag testimage:v1 hemgit/testimage:v1'
 withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'password', usernameVariable: 'username')]) {
 sh 'echo $password | docker login -u $username --password-stdin'
   sh 'docker push hemgit/testimage:v1'
   
}
        
      }
    }

     stage('deploy to stage ') {
    steps {
        withCredentials([file(credentialsId: 'kops', variable: 'KUBECONFIG')]) {
            sh '''
            //export KUBECONFIG=$kubeconfig
               kubectl apply -f k8s-code/staging/namespace/
               kubectl apply -f k8s-code/staging/app/
            '''
        }
    }
}

    stage('approval'){
      steps{
      echo "Approval State"
    timeout(time: 7, unit: 'DAYS') {
    input message: 'Do you want to deploy?', submitter: 'admin'
}
    }
    }

   stage('deploy to prod ') {
    steps {
        withCredentials([file(credentialsId: 'kops', variable: 'KUBECONFIG')]) {
            sh '''
            //export KUBECONFIG=$kubeconfig
               kubectl apply -f k8s-code/prod/namespace/
               kubectl apply -f k8s-code/prod/app/
            '''
        }
    }
}
    
  }
  }

