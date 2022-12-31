pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=bullfrig1 -Dsonar.organization=bullfrig -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=b4bb3bb7ef14f38e00e80b3b48465036e91f664c'
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

	stage('Kubernetes Deployment') {
	   steps {
	      withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubelogin', namespace: '', serverUrl: '') {
		  sh "kubectl apply -f deployment.yaml"
		}
	      }
   	    } 
	   
	stage ('wait_for_testing'){
	   steps {
		   sh 'pwd; sleep 180; echo "Application Has been deployed on K8S"'
	   	}
	   }
	   
	stage('RunDASTUsingZAP') {
          steps {
		    withKubeConfig([credentialsId: 'kubelogin']) {
				sh('zap.sh -cmd -quickurl http://$(kubectl get services/asgbuggy -o json| jq -r ".status.loadBalancer.ingress[] | .hostname") -quickprogress -quickout ${WORKSPACE}/zap_report.html')
				archiveArtifacts artifacts: 'zap_report.html'
		    }
	     }
        } 
   }
}
       

	
	
	
	
