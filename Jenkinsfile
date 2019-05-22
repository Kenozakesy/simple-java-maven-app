pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
		stage('Test') { 
            steps {
                sh 'mvn test' 
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml' 
                    
                }
                success {
                    slackSend channel: '#jenkins',
                    color: 'green',
                    message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} UNITTEST SUCCEEDED \n More info at: ${env.BUILD_URL}"
                }
                failure {
                    slackSend channel: '#jenkins',
                    color: 'red',
                    message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} UNITTEST FAILED \n More info at: ${env.BUILD_URL}"
                }
            }
          
        }
    }
   
}