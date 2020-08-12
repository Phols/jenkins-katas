pipeline {
  agent any
  stages {
    stage('Clone down') {
      agent any
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
            }

          }
          steps {
            unstash 'code'
            skipDefaultCheckout true
            sh 'ci/build-app.sh'
            archiveArtifacts 'app/build/libs/'
            stash 'code'
            deleteDir()
          }
        }

        stage('Test App') {
          agent {
            docker {
              image 'gradle:jdk11'
            }

          }
          steps {
            unstash 'code'
            sh 'ci/unit-test-app.sh'
            junit 'app/build/test-results/test/TEST-*.xml'
          }
        }

      }
    }

    stage('Build docker app') {
      agent any
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code'
        sh 'ci/build-docker.sh'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        stash 'code'
      }
    }

    stage('Push docker app') {
      agent any
      when {
        branch 'master'
      }
      environment {
        DOCKERCREDS = credentials('docker_login')
      }
      steps {
        unstash 'code'
        sh 'echo "$DOCKERCREDS_PSW" | docker login -u "$DOCKERCREDS_USR" --password-stdin'
        sh 'ci/push-docker.sh'
        stash 'code'
      }
    }

    stage('component test') {
      when {
        not {
          branch 'Dev/*'
        }

      }
      steps {
        sh 'ci/component-test.sh'
      }
    }

  }
  environment {
    docker_username = 'phols'
  }
  post {
    always {
      deleteDir()
    }

  }
}