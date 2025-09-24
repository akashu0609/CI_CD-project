node {
    // reference to maven
    // ** NOTE: This 'maven-3.5.2' Maven tool must be configured in the Jenkins Global Configuration.   
    def mvnHome = tool 'maven-3.5.2'

    // holds reference to docker image
    def dockerImage
    // ip address of the docker private repository(nexus)
 
    def dockerImageTag = "devopsexample${env.BUILD_NUMBER}"
    
    stage('Clone Repo') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/akashu0609/CI_CD-project.git'
      // Get the Maven tool.
      // ** NOTE: This 'maven-3.5.2' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'maven-3.5.2'
    }    
  
    stage('Build Project') {
      // build project via maven
      sh "'${mvnHome}/bin/mvn' clean install"
    }
		
    stage('Build Docker Image') {
      // build docker image
      dockerImage = docker.build("devopsexample:${env.BUILD_NUMBER}")
    }
   	  
    stage('Deploy Docker Image and login'){
      
      echo "Docker Image Tag Name: ${dockerImageTag}"
	  
        sh "docker images"
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                  usernameVariable: 'DOCKER_USER',
                                  passwordVariable: 'DOCKER_PASS')]) {
    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
} // put PWD in the jenkins credentials to prevent crashing jenkins server
	
}
    stage('Docker push'){
       // docker images | awk '{print $3}' | awk 'NR==2'
	// sh "docker images | awk '{print $3}' | awk 'NR==2'"
	//sh echo "Enter the docker lattest imageID"
	//sh "read imageid"
	   sh "docker tag ${dockerImage.imageName()} akash/myapplication:${env.BUILD_NUMBER}" //use the dockerImage object you already built
        sh "docker push akash/myapplication:${env.BUILD_NUMBER}"  //Best practice: push with a unique tag e.g., build number or git commit hash
  }

 post {
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
        success {
            echo "✅ Build and push succeeded!"
        }
        failure {
            echo "❌ Build failed. Please check logs."
        }
    }
}
