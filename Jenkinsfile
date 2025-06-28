pipeline {
     agent { label 'slave1' }

		environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
                git 'https://github.com/surajw8380/star-agile-health-care.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Perform Application Build'
                sh 'mvn clean package'
            }
        }
        
        stage('Docker Build') {
            steps {
                echo 'Perform Docker Build'
				sh "docker build -t suraj838098/medicure-project:1 ."
				sh 'docker image list'
    
           }
        }
        
        stage('Login to Dockerhub') {
            steps {
                echo 'Login to DockerHub'				
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                
            }
        }
        
        stage('Publish the Image to Dockerhub') {
            steps {
                echo 'Publish to DockerHub'
				sh "docker push suraj838098/medicure-project:1"                
            }
        }
        
        stage('Deploy the Application to kubernetes cluster Test') {
            steps {
                echo 'Deploy on kubernetes test Server'
				script {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'KubernetesMaster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubedeploytestserver.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'kubedeploytestserver.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
				    
				}              
            }
        }
        
        stage('Deploy the Application to kubernetes cluster Prod') {
            steps {
                echo 'Deploy on kubernetes Prod Server'
				script {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'KubernetesMaster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubedeployprodserver.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'kubedeployprodserver.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
				    
				}              
            }
        }
        
        stage('Deploy the Application to SlaveNode') {
            steps {
                echo 'Deploy on SlaveNode using Ansible Playbook via Publish over SSH'
				script {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'AnsibleController', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /etc/ansible/playbooks/ansible-playbook.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'ansible-playbook.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
				    
				}              
            }
        }
    }
}
