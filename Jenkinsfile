pipeline{
    agent any
    environment{
        DOCKERHUB_USERNAME= "kentable"
        APP_NAME="gitops-app"
        IMAGE_TAG="${BUILD_NUMBER}"
        IMAGE_NAME="${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS="dockerhub"
    }
    stages{
        stage('Clean workspace area'){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage ('Check-Git-Secrets') {
		    steps {
                script{
                    sh 'rm trufflesecurity/trufflehog:latest || true'
                    sh 'docker run --rm -t -v "$PWD:/pwd" trufflesecurity/trufflehog:latest github --repo https://github.com/kennymath/github-ci-project2.git --debug > trufflehog'
                 //   docker run --rm -t -v "$PWD:/pwd" trufflesecurity/trufflehog:latest

                }
	         
	       }
        }
    }
}    