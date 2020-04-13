pipeline {
    agent any

    triggers {
        pollSCM('*/5 * * * 1-5')
    }
    options {
        skipDefaultCheckout(true)
        // Keep the 10 most recent builds
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
    }
    environment {
      PATH="/var/lib/jenkins/miniconda3/bin:$PATH"
    }

    stages {

        stage ("Code pull"){
            steps{
                checkout scm
            }
        }
        stage('Build environment') {
            steps {
                sh '''#conda create --yes -n ${BUILD_TAG} python
                      #source activate ${BUILD_TAG} 
                      #pip install -r requirements.txt
                      which python
                      which pip
                      pip list
                    '''
            }
        }
        stage('Unit Tests') {
            steps {
                sh '''#source activate ${BUILD_TAG} 
                       python -m pytest --verbose --junit-xml test-reports/results.xml
                    '''
            }
            post {
                always {
                    // Archive unit tests for the future
                    junit allowEmptyResults: true, testResults: 'test-reports/results.xml', fingerprint: true
                }
            }
        }
    }
    post {
        always {
            sh 'conda remove --yes -n ${BUILD_TAG} --all'
        }
        filure {
            echo "Send e-mail, when failed"
        }
    }
}