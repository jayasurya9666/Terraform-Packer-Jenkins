pipeline {
    agent any
    stages {

        stage('Clone Repo') {
          steps {
            sh 'rm -rf Terraform-Packer-Jenkins'
            sh 'git clone https://github.com/jayasurya9666/Terraform-Packer-Jenkins.git'
            }
        }
		
		stage('Packer Build AMI') {
          steps {
            
            sh 'pwd'
            sh 'ls -al'
            sh 'cp /var/lib/jenkins/workspace/Pipeline-1/Terraform-Packer-Jenkins/*.* .'
            sh 'ls -al'
            sh 'packer build packer.json'
            }
        }
				
		stage('Deploy EC2 Server') {
          steps {
		    sh 'terraform init'
            sh 'terraform apply --auto-approve'
            }
        }

        stage('Build Docker Image') {
          steps {
            sh 'cd /var/lib/jenkins/workspace/Pipeline-1/Terraform-Packer-Jenkins'
            sh 'cp  /var/lib/jenkins/workspace/Pipeline-1/Terraform-Packer-Jenkins/* /var/lib/jenkins/workspace/Pipeline-1'
            sh 'docker build -t jayasurya/terraform-docker:${BUILD_NUMBER} .'
            }
        }

        stage('Push Image to Docker Hub') {
          steps {
           sh    'docker push jayasurya/terraform-docker:${BUILD_NUMBER}'
           }
        }

        stage('Deploy to Docker Host') {
          steps {
		    sh 'sleep 10s'
            sh    'docker -H tcp://10.10.1.177:2375 stop prodwebapp1 || true'
            sh    'docker -H tcp://10.10.1.177:2375 run --rm -dit --name prodwebapp1 --hostname prodwebapp1 -p 8000:80 jayasurya/terraform-docker:${BUILD_NUMBER}'
            }
        }

        stage('Check WebApp Rechability') {
          steps {
          sh 'sleep 10s'
          sh ' curl http://10.1.1.111:8000'
          }
        }

    }
}
