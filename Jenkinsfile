node{
    
    def mavenHome, mavenCMD, docker, tag, dockerHubUser, containerName, httpPort = ""
    
    stage('Environment Setup'){
        echo 'Setting up Environment'
        mavenHome = tool name: 'maven 3.6.3' , type: 'maven'
        mavenCMD = "${mavenHome}/bin/mvn"
        tag="1.0"
	dockerHubUser="ghitar1"
	containerName="asi-insurance"
	httpPort="8081"
    }
    
    stage('Code Checkout') {
        try{
            checkout scm
        }
        catch(Exception exc) {
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
    
    stage('Build Process') {
        sh "${mavenCMD} clean package"        
    }
     
    stage('Build Docker Image') {
        echo 'Creating Docker image'
        sh "docker build -t $dockerHubUser/$containerName:$tag --pull --no-cache ."
    }
	
    stage('Publishing Image to DockerHub') {
        echo 'Push docker image to DockerHub'
        withCredentials([usernamePassword(credentialsId: 'dockerhub_token', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
		sh "docker login -u $dockerUser -p $dockerPassword"
		sh "docker push $dockerUser/$containerName:$tag"
		echo "Image push complete"
        }
    }    

    stage('Deploy') {
        sh '''
            echo "Start the container on remote"
            sudo ssh -t -i /home/derekmcbridegma/derekskey.pem ec2-user@44.212.74.12<<EOT
            sudo docker rm asi -f
            sudo docker login -u ghitar1 -p dckr_pat_wJKpQ4vpiPFzDRGhyDI653SbBP
            sudo docker pull ghitar1/asi-insurance:1.0
            sudo docker run -d --rm -p 8081:8081 --name asi ghitar1/asi-insurance:1.0
            exit
            EOT
            echo "finished trying ssh"
        '''
    }
}
