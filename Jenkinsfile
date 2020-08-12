pipeline {
  agent any
  stages {
    stage('Parallel execution') {
      parallel {
        stage('Say Hello') {
          steps {
            sh 'echo "hello world"'
          }
        }

        stage('Build App') {
          agent {
            docker {
              image 'gradle:jdk11'
              args 'skipDefaultCheckout(true)'
            }

          }
          steps {
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

      }
    }

    stage('Clone down') {
      agent {
        node {
          label 'host'
        }

      }
      steps {
        stash(name: 'code', excludes: '/.git')
      }
    }

  }
}