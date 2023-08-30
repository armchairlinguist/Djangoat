@Library('semgrep') _

pipeline {
  agent any
  options {
    skipDefaultCheckout(true)
  }
  environment {
    // Required for a Semgrep Cloud Platform-connected scan:
    SEMGREP_APP_TOKEN = credentials('SEMGREP_APP_TOKEN')
    // Set repo name to expected format
    REPO_NAME = "armchairlinguist/Djangoat"
  }
  stages {
    stage('Print-Vars') {
      steps {
        sh 'printenv | sort'
      }
    }
    stage("Checkout") {
      steps {
        cleanWs()
        script {
          if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'main') {
            checkoutRepo(env.REPO_NAME, "master", 1, "master", "https://github.com/")
          } else {
            checkoutRepo(env.REPO_NAME, env.CHANGE_BRANCH, 100, "master", "https://github.com/")
          }
        }
      }
    }
    stage('semgrep-diff-scan') {
      when {
        branch "PR-*"
      }
      steps {
        sh '''chmod -R a+w ${WORKSPACE}/${REPO_NAME}
              cd ${WORKSPACE}/${REPO_NAME}
              docker pull returntocorp/semgrep && \
              docker run \
              -e SEMGREP_APP_TOKEN=$SEMGREP_APP_TOKEN \
              -e SEMGREP_REPO_NAME=${REPO_NAME} \
              -e SEMGREP_BASELINE_REF=$(git merge-base remotes/origin/master HEAD) \
              -e SEMGREP_BRANCH=$CHANGE_BRANCH \
              -v "$(pwd):$(pwd)" --workdir $(pwd) \
              returntocorp/semgrep semgrep ci
           '''      
      }
    }
    stage('semgrep-scan') {
      when {
        branch "master"
      }
      steps {
        sh '''docker pull returntocorp/semgrep && \
            docker run \
            -e SEMGREP_APP_TOKEN=$SEMGREP_APP_TOKEN \
            -e SEMGREP_REPO_NAME=$SEMGREP_REPO_NAME \
            -v "$(pwd):$(pwd)" --workdir $(pwd) \
            returntocorp/semgrep semgrep ci '''      
      }
    }
  }
}
