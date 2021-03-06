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
					sh 'docker build -t cbirdsey1/devopscapstone .'
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
		stage('Create blue cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh '''
						aws configure list
						eksctl create cluster \
						--name blue \
						--version 1.14 \
						--nodegroup-name standard-workers \
						--node-type t2.micro \
						--nodes 1 \
						--nodes-min 1 \
						--nodes-max 4 \
						--node-ami auto \
						--region us-west-2
					'''
				}
			}
		}
		stage('Create green cluster') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh '''
						eksctl create cluster \
						--name green \
						--version 1.14 \
						--nodegroup-name standard-workers \
						--node-type t2.micro \
						--nodes 1 \
						--nodes-min 1 \
						--nodes-max 4 \
						--node-ami auto \
						--region us-west-2
					'''
				}
			}
		}
		stage('Create conf file cluster blue') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh 'aws eks --region us-west-2 update-kubeconfig --name blue'
				}
			}
		}
		stage('Create conf file cluster green') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh 'aws eks --region us-west-2 update-kubeconfig --name green'
				}
			}
		}
		stage('Deploy blue container') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh 'kubectl apply -f ./blue-controller.yaml'
				}
			}
		}
		stage('Deploy green container') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh 'kubectl apply -f ./green-controller.yaml'
				}
			}
		}
		stage('Create blue service') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh 'kubectl apply -f ./blue-service.yaml'
				}
			}
		}
 		stage('Create green service') {
			steps {
				withAWS(region:'us-west-2', credentials:'jenkins') {
					sh 'kubectl apply -f ./green-service.yaml'
				}
			}
		}
    }
}

