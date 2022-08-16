ports = [ 81, 82, 83, 84, 85 ]

pipeline {
    agent {
        node {
            label 'slave'
        }
    }
    parameters {
    	string(name: 'serverIP', defaultValue: 'None', description: 'Enter target Host IP ')
	string(name: 'targetHost', defaultValue: 'None', description: 'Enter target host for deployment ')
	string(name: 'dockerUser', defaultValue: 'None', description: 'Enter Docker user name ')
    }
    environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    }
    stages {
        stage('SCM checkout'){
        	steps {
			git "https://github.com/pavanshivanand/First_Pipeline.git"
            	}
	}
	stage('Remove old containers'){
		steps {
			sh "if [ `docker ps -a -q|wc -l` -gt 0 ]; then docker rm -f \$(docker ps -a -q);fi"
		}
	}
	stage('Build deployment image'){
		steps {
			sh "docker build C:\jenkins\workspace\${JOB_NAME} -t ${dockerUser}/devopsdemo"
		}
	}
	stage('Push Image'){
		steps {
			sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login --username ${dockerUser} --password-stdin"
			sh "docker push ${dockerUser}/devopsdemo:latest"
		}
	}
	stage('Deploy website on containers') {
		steps {
			sh 'ansible-playbook docker.yaml -e "hostname=${targetHost}"'
		}
	}
	stage('Test the website') {
		steps {
			test_web(ports,serverIP)
		}
	}
    }
}

def test_web(ports,serverIP) {
    script {
     ports.each { entry ->                        
	    sh "curl -I http://${serverIP}:${entry}"
     }
    //for (int i = 0; i < port.size(); i++) {
    //    sh "curl -I http://34.125.198.147:${port[i]}"
    //}
    }
}
