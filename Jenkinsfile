pipeline
{

agent {
  label 'DevServer'
} 
parameters {
  string defaultValue: 'Mohammed', name: 'LASTNAME'
}
environment {
    NAME = "Shariq"
}
tools {
  maven 'mymaven'
}
stages{

   stage('build')
   {
      
     steps {
      sh 'mvn clean package'
      echo "hello $NAME ${params.LASTNAME}"
     }
   }
   stage('test')
   {
     parallel {
        stage('testA')
        {
            steps{
                   echo "This is test A"
            }
        }
        stage('testB')
        {
            steps{
            echo "This is test B"
            }
        }
     }
   } 
   post {
     success {
         archiveArtifacts artifacts: '**/target/*.war'
              }
   }
}
}
    