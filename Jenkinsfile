pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=bullfrig1 -Dsonar.organization=bullfrig -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=4a2f291c7a515000e01d3a0f53d801a26097f0f5'
			}
        } 

    stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }   

      stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
			 
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{						
                    docker.withRegistry('https://467290638204.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
	
                    }
                }
            }
	}

	stage('Kubernetes Deployment of ASG Bugg Web Application') {
	   steps {
	      withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubelogin', namespace: '', serverUrl: '') {
		  sh "kubectl apply -f deployment.yaml"
		}
	      }
   	    }  

	}
}
       

	
	
	
	
