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
        echo 'Start the container on remote'
        sh "sudo ssh -i /home/derekmcbridegma/derekskey.pem ec2-user@54.90.84.162 -p 22"
        echo 'finished trying ssh'
    }

//    stage('Deploy using Ansible') {
//        echo 'Run container from EC2 Server'
//        ansiblePlaybook(
//            inventory: '',
//            installation: 'ansible',
//            limit: '',
//            playbook: 'ansible-playbook.yml',
//            extras: ''
//        )
//}

//stage("DEPLOY CONTAINER"){
//	script {
//		sh """
//		#!/bin/bash
//		sudo ssh -i "/home/derekmcbridegma/derekskey.pem" ec2-user@ec2-54-90-84-162.compute-1.amazonaws.com -S "/bin/bash" << EOF
//		sudo docker login -u ghitar1 -p dckr_pat_wJKpQ4vpiPFzDRGhyDI653SbBPw
//		sudo docker pull $dockerHubUser/$containerName:$tag
//		sudo docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag
//		exit 0
//		<< EOF
//		"""
//	}
//}

	
    //stage('Docker Container Deployment'){
//	sh "docker rm $containerName -f"
//	sh "docker pull $dockerHubUser/$containerName:$tag"
//	sh "docker run -d --rm -p $httpPort:$httpPort --name $containerName $dockerHubUser/$containerName:$tag"
//	echo "Application started on port: ${httpPort} (http)"
//    }
}
