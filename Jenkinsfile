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
				sh "docker tag siva/eta-app:${BUILD_NUMBER} loksaieta/loksai-eta-app:latest"
            }
        }
        stage('Deploy to QA Server') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'QA_Server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: 'target/', sourceFiles: 'target/mvn-hello-world.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		}
        }
	}
    }
}
