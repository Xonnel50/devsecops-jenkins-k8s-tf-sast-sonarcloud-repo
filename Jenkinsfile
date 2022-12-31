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
       

	
	
	
	
