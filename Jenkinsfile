
echo 'Begin'

pipeline {
    agent any
	tools {nodejs "Node"}
	
    stages {
        stage('build') {
            steps {
                sh 'npm --version'
            }
        }
        stage('Initialize') {
            steps {
                sh 'npm install'
            }
        }
    }
}

node {
    def app

    stage('Clone repository') {
        /* Cloning the Repository to our Workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image */

        app = docker.build("jeffkiller/ui_project_ci")
    }

    stage('Test image') {
        
        app.inside {
            echo "Tests passed"
        }
    }

    stage('Push image') {
        /* 
			You would need to first register with DockerHub before you can push images to your account
		*/
        docker.withRegistry('https://registry.hub.docker.com', 'jeffkiller-dockerhub') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
            } 
                echo "Trying to Push Docker Build to DockerHub"
    }
stage ('Deploy to Dev') {

def dockerRun = 'sudo docker run -p 3030:3030 --name Movie-UI -d jeffkiller/ui_project_ci:latest'

sshagent(['deploy-to-dev-docker']) {

sh "ssh -o StrictHostKeyChecking=no admin@18.205.104.158 ${dockerRun}"

}

}
}
