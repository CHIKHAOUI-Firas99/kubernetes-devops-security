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
          }
          stage('Mutation Tests - PIT') {
              steps {
                sh "mvn org.pitest:pitest-maven:mutationCoverage"
              }
              post {
                always {
                  pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
                }
              }
            }
          stage('Sonarqube') {
              steps {
                sh "mvn clean verify sonar:sonar \
                    -Dsonar.projectKey=numeric-application \
                    -Dsonar.host.url=http://dev-sec-ops-demo.eastus.cloudapp.azure.com:9000 \
                    -Dsonar.login=sqp_f113bce83a9398abfe9af951780a6da8ec1d4c14"
              }  



          stage('Docker buid and push'){
            steps{
              withDockerRegistry([credentialsId:"docker-hub", url: ""]){
              sh 'printenv'
              sh 'docker build -t firaschikhaoui/numeric-app:""$GIT_COMMIT"" .'
              sh 'docker push firaschikhaoui/numeric-app:""$GIT_COMMIT""'
             }
           }
          }


          stage('Kubernetes deployment'){
            steps{
            withKubeConfig([credentialsId: 'kubeconfig']){
              sh "sed -i 's#replace#firaschikhaoui/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
              sh "kubectl apply -f k8s_deployment_service.yaml"
            }
            }

          }

        }
}
