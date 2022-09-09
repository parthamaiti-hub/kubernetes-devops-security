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
        
        stage('Sonarqube scan SAST') {
            steps {
              bat "mvn clean verify sonar:sonar -Dsonar.projectKey=appsample -Dsonar.host.url=http://localhost:9000 -Dsonar.login=sqp_b90e7c99939f3711876f6e2bda533669d27cbe25"

            }
                           
        } 
        stage('Vulnerability Scan Dependencies check') {
            steps {
              bat "mvn dependency-check:check"
            }
            post {
               always {
                 dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'       
                 			         
               }
            }
                
        } 
 
        
        stage('Docker build and Push') {
	      steps{
	        withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
	          bat 'docker build -t parthamaiti/numeric-app:"test" .'
	          bat 'docker push parthamaiti/numeric-app:"test"'
	        }
	      }
	    }
	    
	    stage(' Find kubernetes details') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                bat 'kubectl get all'
            }
            }
        }
	    
	    stage('Kubernetes Deployment -DEV')  {
	      steps{
	        withKubeConfig([credentialsId: 'kubeconfig']) {
	           bat "kubectl apply -f k8s_deployment_service.yaml"
	        }
	      }
	    }
    
          
    }
}