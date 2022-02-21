pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="975639581513"
        AWS_DEFAULT_REGION="ap-south-1" 
	CLUSTER_NAME="ecs-demo-cluster"
	SERVICE_NAME="ecs-demo-cluster-service"
	TASK_DEFINITION_NAME="ecs-demo-cluster-task"
	DESIRED_COUNT="1"
        IMAGE_REPO_NAME="image"
        IMAGE_TAG="${env.BUILD_ID}"
        REPOSITORY_URI = "http://3.110.241.185:1111/repository/ediig-docker-hosted/"
	registryCredential = "nexus-admin"
	registryCredentialAws = "AdminIAM"
	    
    }
   
    stages {

    // Tests
    stage('Unit Tests') {
      steps{
        script {
          sh 'npm install'
	  sh 'npm test -- --watchAll=false'
        }
      }
    }
        
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
   
    // Uploading Docker images into Nexus
    stage('Pushing to Nexus') {
     steps{  
         script {
			docker.withRegistry("REPOSITORY_URI + registryCredential) {
                    	dockerImage.push()
                	}
         }
        }
      }
      
    stage('Deploy to ECS') {
     steps{
            withAWS(credentials: registryCredentialAws, region: "${AWS_DEFAULT_REGION}") {
                script {
			sh './script.sh'
                }
            } 
        }
      }      
      
    }
}
