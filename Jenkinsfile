pipeline {
  agent any
  environment{
        registry = "parthamaiti/numeric-app"
        registryCredential = 'docker-hub'        
    }
  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
       }
       stage('unit test') {
            steps {
              sh "mvn test"
            }
           post {
               always {
                 junit 'target/surefire-reports/*.xml'
                 jacoco execPattern: 'target/jacoco.exec'
               }
            }
                
        } 
         /*
        stage('Mutation testing using PIT') {
            steps {
              bat "mvn org.pitest:pitest-maven:mutationCoverage"
            }
            post {
               always {
                 pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'       
                 			         
               }
            }
                
        } 
        */ 
        /*
        stage('Sonarqube scan SAST') {
            steps {
              sh "mvn clean verify sonar:sonar -Dsonar.projectKey=appsample -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_148b7e3ab3857e43ff35306a582c545fb0ade313"

            }
                           
        } 
        */
        /*
        stage('Vulnerability Scan Dependencies check') {
            steps {
              sh "mvn dependency-check:check"
            }
            post {
               always {
                 dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'       
                 			         
               }
            }
                
        } 
        */
        
        stage('Docker build and Push') {
	      steps{
	        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
	          sh 'docker build -t parthamaiti/numeric-app:"test" .'
	          sh 'docker push parthamaiti/numeric-app:"test"'
	        }
	      }
	    }
	    
	    stage(' Find kubernetes details') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                sh 'kubectl get all'
            }
            }
        }
	    
	    stage('Kubernetes Deployment -DEV')  {
	      steps{
	        withKubeConfig([credentialsId: 'kubeconfig']) {
	           sh "kubectl apply -f k8s_deployment_service.yaml"
	        }
	      }
	    }
    
          
    }
	post {
    success {
      mail to: "parthamaiti.conn@gmail.com", subject:"SUCCESS: ${currentBuild.fullDisplayName}", body: "Yay, we passed."
    }
    failure {
      mail to: "parthamaiti.conn@gmail.com", subject:"FAILURE: ${currentBuild.fullDisplayName}", body: "Boo, we failed."
    }
  }
}
