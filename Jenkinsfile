pipeline {
  agent any

  triggers {
    pollSCM('*/5 * * * 1-5')
  }

  stages {
    
    stage('Code Pull') {
      steps {
        echo "Checking out the code"
        checkout scm   
      }
    }
    
    stage('Build Environment') {
      steps {
        echo "Building the Virtual Environment"
        sh 'pip3 install -r requirements.txt'
      }
    }
    
    stage('Execute Application') {
      steps { 
        echo "Executing the application - Build Tag - ${BUILD_TAG}"
        sh "./app.py &"
      }
    }
    
    stage('Execute UnitTest') {
      steps {
        sh 'python test.py'
      }  
    }

    stage('Execute Deploy') {
      steps {
        echo "Deploying the package"
      }
    }
  }
  post {
    always {
        junit 'test-reports/*.xml'
    }
    success {
        echo 'The build is validated successfully'
        emailext (
          subject: "Job '${env.JOB_NAME} ${env.BUILD_NUMBER}'",
          body: """<p>Check console output at <a href="${env.BUILD_URL}">${env.JOB_NAME}</a></p>""",
          to: "bharat.sarvan@247.ai",
          from: "jenkins@247.ai"
        )
    }
    failure {
        echo 'Tests failed for the current package'
    }
    unstable {
        echo 'This will run only if the run was marked as unstable'
    }
    changed {
        echo 'This will run only if the state of the Pipeline has changed'
        echo 'For example, if the Pipeline was previously failing but is now successful'
    }
  }
}
