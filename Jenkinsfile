pipeline {
  agent any

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
        stage('Docker buikd and push') {
          steps {
          	bat 'printenv'
          	bat 'docker build -t parthamaiti/numeric-app:""$GIT_COMMIT"" .'
          	bat 'docker push parthamaiti/numeric-app:""$GIT_COMMIT""'
          }
        }
          
    }
}