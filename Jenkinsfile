@Library("shared-library") _
pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: maven
            image: 'maven:alpine'
            command:
            - cat
            tty: true
        '''
    }
  }
  
  stages {
    
        stage('startTime stage') {
            steps {
               buildStart()
            }
        }  
      
        stage('clean and build') {
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
            }
        }
        stage('deploy') {
            steps {
                sh './scripts/deliver.sh'
            }
        }
    
        stage('endTime stage') {
            steps {
                buildEnd()
            }
        }  
          
    post {
            success {
                emailext (
                    subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                    body: """SUCCESSFUL: Job '${JOB_NAME} [${BUILD_NUMBER}]':
                    Check console output at ${BUILD_URL}""",
                    to: 'ted.fenn@concanon.com'
                )
            }
            failure {
                emailext (
                    subject: "FAILURE: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                    body: """FAULURE: Job '${JOB_NAME} [${BUILD_NUMBER}]':
                    Check console output at ${BUILD_URL}""",
                    to: 'ted.fenn@concanon.com'
                )
            }    
    }
}
