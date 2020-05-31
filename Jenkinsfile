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
 
def executeIn(String environment, String script) {
    sh "source ${environment}/bin/activate && " + script
}
 
// alternative workaround
env.VENV_PATH = "${JENKINS_HOME}/.virtualenv/${JOB_NAME}/venv"

def virtualEnv(String rebuild){
    withEnv(["PATH+VEX=~/.local/bin"]){
        if(rebuild == "true") {
            sh "rm -rf ${env.VENV_PATH}"
            sh "echo 'rebuild is true'"
        }
        sh returnStatus: true, script: "virtualenv ${env.VENV_PATH}"
    }
}

def runCmd(String pyCmd){
    withEnv(["PATH+VEX=~/.local/bin"]){
        sh returnStatus: true, script: "vex --path=${env.VENV_PATH} ${pyCmd}"
    }
}


