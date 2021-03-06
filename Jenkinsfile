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
        echo "Creating the Virtual Environment"
        createVirtualEnv 'env'
        executeIn 'env', 'pip3 install -r requirements.txt'
      }
    }
    
    stage('Execute Application') {
      steps { 
        echo "Executing the application - Build Tag - ${BUILD_TAG}"
        executeIn 'env', './app.py &'
      }
    }
    
    stage('Execute UnitTest') {
      steps {
        echo "Executing the Unit Test"
        executeIn 'env', 'python test.py'
        echo "Deactivating and Deleting the Virtual Environment"
        deleteVirtualEnv 'env'
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
            to: "bharatsarvan@gmail.com",
            from: "bharatsarvan@gmail.com"
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

// one of the workaround
def createVirtualEnv(String name) {
    sh "virtualenv ${name}"
}

def deleteVirtualEnv(String name) {
  sh ". ${name}/bin/activate && deactivate"
  sh "rm -rf ${name}"
}

def executeIn(String environment, String script) {
    sh ". ${environment}/bin/activate && " + script
}


