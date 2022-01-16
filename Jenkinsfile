// A DECLARATIVE PIPELINE

pipeline {
    
	tools {
	    jdk 'myJDK_11'
	}
	
	agent any
	
	environment {
		dockerHome = tool 'myDocker'
		mavenHome = tool 'myMaven'
		PATH = "$dockerHome/bin:$mavenHome/bin:$javaHome/bin:$PATH"
	}
	
	stages {
		stage('Implicit Checkout & Build') {
			steps {
				echo "Build"
				echo "PATH - $PATH"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUILD_TAG - $env.BUILD_TAG"
				
				sh "mvn clean compile"
			}
		}
		stage('Test') {
			steps {
				sh "mvn test"
			}
		}
		stage('Integration Test') {
			steps {
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}
		stage('Package') {
			steps {
                sh "mvn package -DskipTests"
			}
		}
		stage('Build Docker Image') {
			steps {
				// Run jenkins inside a docker container
				script {
					// dockerImage = docker.build("in28min/currency-exchange-devops:${env.BUILD_TAG}"")
				    dockerImage = docker.build('in28min/currency-exchange-devops:01')
				}
			}
		}
		stage('Push Docker Image') {
			steps {
                script {
                    docker.withRegistry('', 'dockerhub') {
                        dockerImage.push();
                        dockerImage.push('latest');
                    }
                }
			}
		}
	} 
	post {
		always {
			echo "This block of code will always execute"
		}
		success {
			echo "This block of code only runs if the job is successful"
		}
		failure {
			echo "This block of code only runs if the job fails at a specific step"
		}
	}
}