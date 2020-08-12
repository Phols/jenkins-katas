pipeline {
  agent any
  stages {
    stage('Clone down') {
      agent {
        node {
          label 'Host'
        }

      }
      steps {
        stash(name: 'code', excludes: '.git')
      }
    }

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
            stash(name: 'code', includes: '.git')
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            sh 'ls'
            deleteDir()
            sh 'ls'
          }
        }

      }
    }

  }
}