pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=dev-sec-ops -Dsonar.organization=dev-sec-ops -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=47d624304c15bb753a86edf0be6221cefa9d74f8'
			}
        } 
  }
}
