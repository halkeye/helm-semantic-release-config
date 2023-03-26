pipeline {
  agent any
  options {
    timeout(time: 60, unit: 'MINUTES')
    ansiColor('xterm')
    disableConcurrentBuilds(abortPrevious: true)
    buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '5', numToKeepStr: '5')
  }

  environment {
    NODE_ENV = 'production'
    TZ = "UTC"
  }

  stages {
    stage('Install Dependencies') {
      environment {
        NODE_ENV = 'development'
      }
      steps {
        sh 'npm ci'
      }
    }

    stage('Release') {
      when {
        allOf {
          anyOf {
            branch "main"
            branch "master"
            branch "alpha"
            branch "beta"
          }
        }
      }
      environment {
        NPM_TOKEN = credentials('npm-halkeye-ci')
      }
      steps {
        script {
          withCredentials([usernamePassword(credentialsId: 'github-app-halkeye',
                usernameVariable: 'GITHUB_APP',
                passwordVariable: 'GITHUB_TOKEN')]) {
            sh 'npx semantic-release --repositoryUrl https://x-access-token:$GITHUB_TOKEN@github.com/jenkins-infra/jenkins-io-components.git'
          }
        }
      }
    }
  }
}
