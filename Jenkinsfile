pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh "mvn sonar:sonar Dsonar.projectKey=Maverick Dsonar.host.url=http://54.90.201.131:9000 Dsonar.login=7f1a5a84a9192ef067a7c459e3c06c9583789fd7" 
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
               withDockerRegistry([credentialsId: "docker", url: ""]) {
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
       

	
	
	
	
