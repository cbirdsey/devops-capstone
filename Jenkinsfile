pipeline {
	 agent any
	 stages {
		 stage('Linting') {
            steps {
                sh 'tidy -q -e *.html'
            }
		 }
		stage('Build image') {
			steps {
				withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
					sh 'docker build -t="cbirdsey1/devopscapstone" github.com/dockerfile/nginx'
				}
			}
        }
		stage('Push image') {
			steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
		        	sh 'docker login -u $USERNAME -p $PASSWORD'
					sh 'docker push cbirdsey1/devopscapstone'
				}
			}
        }
     }
}
