pipeline {
    agent any
    stages {
    	stage('Build') 	{
			steps {
        		sh './gradlew -b build.gradle clean build'
			}
    	}
    	stage('parallel stages') {
    		parallel {
    			stage('Archival') {
				    steps {
        				archiveArtifacts 'build/libs/*.jar'
				    }
    			}
                stage('Test cases') {
                    steps {
                        junit 'build/test-results/test/*.xml'
				    }
                }
            }
        }
         stage('Build Image') {
            steps {
                sh '''
                    docker build --no-cache -t product-catalog-image:latest .
                    docker tag product-catalog-image:latest akmaharshi/product-catalog-image:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker login --username akmaharshi --password sairam123
                    docker push akmaharshi/product-catalog-image:v${BUILD_NUMBER}
                '''
            }
        } 
    }

    post {
        always {
            notify('started')
        }
        failure {
            notify('err')
        }
        success {
            notify('success')
        }
    }    
}

