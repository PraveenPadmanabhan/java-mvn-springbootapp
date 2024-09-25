pipeline {

    agent { label 'agent1' }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	}
	
    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Perform SCM Checkout'
				git 'https://github.com/PraveenPadmanabhan/java-mvn-springbootapp.git'
            }
        }
        stage('Application_Build') {
            steps {
                echo 'Perform Maven Build'
				sh 'mvn -Dmaven.test.failure.ignore=true clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
				sh 'docker version'
				sh "docker build -t devopstraining/sample-web-app:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag devopstraining/sample-web-app:${BUILD_NUMBER} loksaieta/loksai-eta-app:latest"
            }
        }

		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push devopstraining/sample-web-app:latest"
			}
		}
		stage('Deploy to Kubernetes') {
            steps {
				script {
				sshPublisher(publishers: [sshPublisherDesc(configName: 'PoW-Kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f k8sdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
			}	
            }
		}
    }
}
