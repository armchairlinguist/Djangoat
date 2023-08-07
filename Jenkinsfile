@Library('semgrep') _

pipeline {
  agent any
    environment {
      // The following variable is required for a Semgrep Cloud Platform-connected scan:
      SEMGREP_APP_TOKEN = credentials('SEMGREP_APP_TOKEN')
      SEMGREP_BASELINE_REF = "origin/master"
      SEMGREP_BRANCH = "${env.GIT_BRANCH}"
      SEMGREP_COMMIT = "${env.GIT_COMMIT}"
      SEMGREP_REPO_URL = "https://github.com"
      SEMGREP_PR_ID = "${env.ghprbPullId}" // was "${env.CHANGE_ID}"
      SEMGREP_REPO_NAME = env.GIT_URL.replaceFirst(/^https:\/\/github.com\/(.*)$/, '$1')
    }
    stages {
      stage('Print-Vars') {
        steps {
          sh 'printenv | sort'
        }
      }
      stage('Semgrep-Scan') {
        steps {
                script {
                    if (env.ghprbPullId != null) {
                        sh "echo 'PR scan'"
                        semgrepPullRequestScan()
                    }  else {
                        sh "echo 'full scan'"
                        semgrepFullScan()
                    }
                }
            }
        }
    }
}
