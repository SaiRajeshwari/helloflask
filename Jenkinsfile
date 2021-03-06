def workspace
pipeline {
  agent { docker { image 'python:3.7.2' } }
  stages {
    stage('Build') {
      steps {
        sh 'pip install -r requirements.txt'
        echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
        script {
          workspace = pwd()
        }
      }
    }
    stage('Code Analysis') {
      steps {
        sh "echo 'Run Static Code Analysis'"
        build job: 'Code Analysis', parameters: [string(name: 'workspace', value: workspace)]
        script {
          def browsers = ['chrome', 'firefox']
          for (int i = 0; i < browsers.size(); ++i) {
            echo "Analysing the ${browsers[i]} browser"
          }

        }
      }
    }
    /* stage('Test Logstash') {
      steps {
        timestamps {
          logstash{
            echo "Hello Logstash"
          }
        }
      }
    } */
    stage('Test') {
      steps {
        sh 'python test.py'
      }
      post {
        always {
          junit 'test-reports/*.xml'
          /* logstashSend failBuild: false, maxLines: 1000 */
        }
        success {
          echo 'All tests were successful'
          /* mail to: 'sairajeshwari.g@gmail.com',
              subject: "Successful Pipeline: ${currentBuild.fullDisplayName}",
              body: "${env.BUILD_URL} build successfully" */
        }
        failure {
          echo 'Failed test cases'
          /* mail to: 'sairajeshwari.g@gmail.com',
              subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
              body: "Something is wrong with ${env.BUILD_URL}" */
        }
      }
    }
    stage('Deploy') {
      steps {
        sh "echo 'Ready to deploy!'"
        script {
          try {
            build 'Deploy'
          } catch (ex) {
            echo 'caught exception: ' + ex.toString()
            echo 'Failed to Deploy'
          }
        }
        echo "Result: ${currentBuild.currentResult}"
      }
    }

    stage ('Random Success/Fail') {
      steps {
        sh "echo 'Hello'"
        script {
          if (currentBuild.number % 2 == 0) {
            throw new Exception("Something went wrong!")
          }
        }
      }
      post {
        always {
          logstashSend failBuild: false, maxLines: 1000
        }
      }

    }

  }
}
