pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
  }
          stage('unit test - Junit and jacoco') {
            steps {
              sh "mvn test"
            }
            post {
              always{
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec' 
              }
            }
          stage('Docker buid and push'){
            steps{
              docker.withRegistry(, docker-hub)
              sh 'printenv'
              sh 'docker build -t firaschikhaoui/numericapp:""$GIT_commit"" .'
              sh 'docker push firaschikhaoui/numericapp:""$GIT_commit"" '

            }
          }  
  }
}
}
