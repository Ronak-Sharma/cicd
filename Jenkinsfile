pipeline {
    agent {
        node {
            label 'slave'
        }
    }
    parameters {
    	string(name: 'serverIP', defaultValue: 'None', description: 'Enter Server IP.. ')
	string(name: 'servername', defaultValue: 'None', description: 'Enter Ansible slave name ')
	password(name: 'dockerpass', description: 'Enter docker login password ')	    
    }
    stages {
	    stage('checkout'){
		    steps{
		    checkout scm
		    }
	    }
	stage('Remove dockers'){
	    steps {
		sh "if [ `sudo docker ps -a -q|wc -l` -gt 0 ]; then sudo docker rm -f \$(sudo docker ps -a -q);fi"
		}
	}
	stage('Build'){
	    steps {
		    sh "cd ${WORKSPACE}"
		    sh "sudo docker build -t devopsdemo ."
	   }
	}
	stage('Enable service'){
		steps {
		    sh '''
		    sudo docker run -it -p 82:80 -d devopsdemo
		    '''
	        }
	}
	stage('Configure servers with Docker and deploy website') {
            	steps {
			sh '''
			echo "ansible-playbook docker.yaml -e hostname=${servername}"
			'''
            	}
        }
	stage('Install Chrome browser') {
            	steps {
                	sh '''
			echo "ansible-playbook chrome.yaml -e hostname=localhost"
			'''
            	}
        }
	stage ('Testing'){
		steps {
			sh "sudo apt remove python3-pip -y"
			sh "sudo apt install python3-pip -y"
			sh "pip3 install selenium"
			//sh "python3 selenium_test.py ${params.serverIP}"
		}
	}
	stage ('mvn build'){
		steps {
			sh "cd ${WORKSPACE}/javacode/hello_world"
			sh "mvn build"
		}
	}
	    stage ('mvn deploy'){
		steps {
			sh "cd ${WORKSPACE}/javacode/hello_world"
			sh "mvn deploy"
		}
	}
    }
}
