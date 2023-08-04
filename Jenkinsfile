pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven3.9"
    }

    stages {
        stage('SCM Checkout') {
            steps {
                echo 'Checkout Src from github repo'
		git 'https://github.com/siva42641/pipeline.git'
            }
        }
        stage('Maven Build') {
            steps {
                echo 'Perform Maven Build'
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
	stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t siva/eta-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag siva/eta-app:${BUILD_NUMBER} siva42641/eta-app:latest"
            }
        }
	    stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
        stage('Approve - push Image to dockerhub'){
            steps{
                
                //----------------send an approval prompt-------------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required Choose "yes" | "Abort"'
                       }
                //-----------------end approval prompt------------
            }
        }
		stage('Push2DockerHub') {

			steps {
				sh "docker push siva42641/eta-app:latest"
			}
		}
        stage('Approve - Deployment to Kubernetes Cluster'){
            steps{
                
                //----------------send an approval prompt-----------
                script {
                   env.APPROVED_DEPLOY = input message: 'User input required Choose "yes" | "Abort"'
                       }
                //-----------------end approval prompt------------
            }
        }
        stage('Deploy to Kubernetes Cluster') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'kubernetescluster', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f k8smvndeployment.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		}
            }
	}
}
}
    }
}
