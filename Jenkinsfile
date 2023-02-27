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
                    sh 'cat trufflehog'

                }
	         
	       }
        }
        stage ('Source-Composition-Analysis (SCA)') {
		    steps {
		     sh 'rm owasp-* || true'
		     sh 'wget https://raw.githubusercontent.com/devopssecure/webapp/master/owasp-dependency-check.sh'	
		     sh 'chmod +x owasp-dependency-check.sh'
		     sh 'bash owasp-dependency-check.sh'
		     sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
		   }
	   }
        stage('Git Checkout SCM'){
            steps{
                script{
                    git credentialsId: 'GitHub-id',
                    url: 'https://github.com/kennymath/github-ci-project2.git',
                    branch: 'main'
                }
            }
        }
        stage('Build Docker Image'){
            steps{
                script{
                    
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){
            steps{
                script{
                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUIlD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Delete Docker Images'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Updating Kubernetes Deployment File'){
            steps{
                script{
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml

                    """
                }
            }
        }
        stage(' Push the Changed Deployment File Git '){
            steps{
                script{
                    sh """
                     git config --global user.name "kennymath"
                     git config --global user.email mathias.kenneth@yahoo.com
                     git add deployment.yml
                     git commit -m "updated deployment.yml file"
                    """
                    withCredentials([gitUsernamePassword(credentialsId: 'GitHub-id', gitToolName: 'Default')]) {

                     sh "git push https://github.com/kennymath/github-ci-project2.git main"

                    }
                }
            }
        }
    }
}    