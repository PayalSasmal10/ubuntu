pipeline {
	 environment {
	    registry = "payalsasmal/ubuntu_18.04"
	    registryCredential = 'dockerhub'
	    app = ''
	    PROJECT_ID = 'rising-webbing-276809'
        CLUSTER_NAME = 'docker-image'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'gke'

  	}
	agent any
	stages {
	        stage('Clone Repository') {
	        steps {
	            checkout scm
	            }
	        }
	   stage('Build Docker Image') {
	        steps {
		   script {
			    sh "cd /var/jenkins_home/workspace/UbuntuPipeline"
			    sh "docker-compose -p payalsasmalubuntu_18.04:${env.BUILD_ID} build "
		   }
	        
	        }
	   }


	   stage('Push Docker image') {
	        steps {
                   script {
                        docker.withRegistry( '', registryCredential ) {
				sh "docker-compose push"
                        }
	               }

	        }
	   }

	   stage('Deploy to K8S') {
	        steps{
                sh "sed -i 's/ubuntu_18.04:latest/ubuntu_18.04:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml',
                credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            	}



	    }


    }
}
