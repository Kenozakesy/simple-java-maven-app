pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('BUILD') { 
            steps {
                sh 'mvn -B -DskipTests install package' 
            }
        }
		stage('UNIT-TEST') { 
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
                    message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} ${STAGE_NAME} SUCCEEDED \n More info at: ${env.BUILD_URL}"
                }
                failure {
                    slackSend channel: '#jenkins',
                    color: 'red',
                    message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} ${STAGE_NAME} FAILED \n More info at: ${env.BUILD_URL}"
                }
            }
        }
        stage('Sonarqube') {
            environment {
                scannerHome = tool 'SonarQubeScanner'
            }

            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }

                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
    
   
}