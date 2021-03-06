pipeline {
  agent any
  stages {

    stage('Build') {
      environment {
        HOME = "${WORKSPACE}"
      }
      agent {
        docker {
          image 'px4io/px4-docs:1.0'
        }
      }
      steps {
        sh 'export'
        sh 'gitbook install'
        sh 'gitbook build'
        stash includes: '_book/', name: 'gitbook'
      }
    } // Build

    stage('Deploy') {
      environment {
        GIT_COMMITTER_EMAIL = "bot@pixhawk.org"
        GIT_COMMITTER_NAME = "PX4BuildBot"
      }

      agent {
        docker {
          image 'px4io/px4-docs:1.0'
        }
      }

      steps {
        sh 'export'
        unstash 'gitbook'
        withCredentials([usernamePassword(credentialsId: 'px4buildbot_github', passwordVariable: 'GIT_PASS', usernameVariable: 'GIT_USER')]) {
          sh('git clone --bare https://${GIT_USER}:${GIT_PASS}@github.com/PX4/dev.px4.io.git')
        }
        //sh('cd docs.px4.io; git status')
        //sh('cd docs.px4.io; git push')
      }

      when {
        anyOf {
          branch 'master'
          branch 'pr-jenkins'
        }
      }
    } // Deploy
  } // stages
}

