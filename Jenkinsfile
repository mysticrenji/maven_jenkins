pipeline {
environment {
    registry = "mysticrenji/maven-test"
    registryCredential = 'dockerhub'
        dockerImage = ''
  }  
    agent {
        docker { image 'maven:3-alpine' }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
        stage ('Test') {
               steps {
                sh 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                }
            }
              }
              
              stage('Building image') {
                 agent {
                     label 'slaves'
                 }

		      steps{
		        script {
		          dockerImage = docker.build registry + ":$BUILD_NUMBER"
		        }
		      }
		    }
		    
		    
		     stage('Deploy Image') {
		        agent {
		            label 'slaves'
		        }

		    
		      steps{
		        script {
		          docker.withRegistry( '', registryCredential ) {
		            dockerImage.push()
		          }
		        }
      }
    
    }
    stage('Remove Unused docker image') {
  
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
    
    
		              
              stage('Deliver') { 
            steps {
                sh 'echo Deployment is complete' 
            }
        }

    
    }
}