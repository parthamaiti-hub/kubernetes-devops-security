pipeline {
  agent any
  environment{
        registry = "parthamaiti/numeric-app"
        registryCredential = 'docker-hub'        
    }
  stages {
      stage('Build Artifact') {
            steps {
              bat "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
       }
       stage('unit test') {
            steps {
              bat "mvn test"
            }
            post {
               always {
                 junit 'target/surefire-reports/*.xml'
                 jacoco execPattern: 'target/jacoco.exec'
               }
            }
                
        } 
        
        stage('Docker build and Push') {
	      steps{
	        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
	          bat 'docker build -t parthamaiti-hub/numeric-app:"test" .'
	          bat 'docker push parthamaiti-hub/numeric-app:"test"'
	        }
	      }
	    }
	    
    
          
    }
}