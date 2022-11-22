pipeline {
  agent any
  tools { 
        maven 'Maven3'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=dev-sec-ops -Dsonar.organization=dev-sec-ops -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=18531338b8949301912d0dfaf86f4deb87848629'
			}
        } 
  }
}
