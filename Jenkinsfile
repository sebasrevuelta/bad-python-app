pipeline {
  agent any
    environment {
      // The following variable is required for a Semgrep Cloud Platform-connected scan:
      SEMGREP_APP_TOKEN = credentials('SEMGREP_APP_TOKEN')

      // Uncomment the following line to scan changed 
      // files in PRs or MRs (diff-aware scanning): 
      // SEMGREP_BASELINE_REF = "main"

      // Troubleshooting:

      // Uncomment the following lines if Semgrep Cloud Platform > Findings Page does not create links
      // to the code that generated a finding or if you are not receiving PR or MR comments.
      SEMGREP_JOB_URL = "${BUILD_URL}"
      SEMGREP_COMMIT = "${GIT_COMMIT}"
      SEMGREP_BRANCH = "${GIT_BRANCH}"
      // SEMGREP_REPO_NAME = env.GIT_URL.replaceFirst(/^https:\/\/github.com\/(.*).git$/, '$1')
      SEMGREP_REPO_URL = env.GIT_URL.replaceFirst(/^(.*).git$/,'$1')
      SEMGREP_PR_ID = "${env.CHANGE_ID}"
    }
    stages {
      stage('Print Environment Variables') {
            steps {
                script {
                    // Execute a shell command to print all environment variables
                    sh 'printenv | sort'
                }
            }
      }
      
      stage('Semgrep-Scan') {
        steps {
          script {
            if (env.ghprbPullId != null) {
              sh '''echo *************'''
              sh '''echo Diff Scan '''
              sh '''echo *************'''
              sh '''docker pull returntocorp/semgrep && \
              docker run \
              -e SEMGREP_APP_TOKEN=$SEMGREP_APP_TOKEN \
              -e SEMGREP_PR_ID=env.ghprbPullId \
              -e SEMGREP_BASELINE_REF="main" \
              -v "$(pwd):$(pwd)" --workdir $(pwd) \
              returntocorp/semgrep semgrep ci '''
            }
            else {
              sh '''echo *************'''
              sh '''echo Full Scan '''
              sh '''echo *************'''
              sh '''docker pull returntocorp/semgrep && \
              docker run \
              -e SEMGREP_APP_TOKEN=$SEMGREP_APP_TOKEN \
              -v "$(pwd):$(pwd)" --workdir $(pwd) \
              returntocorp/semgrep semgrep ci '''
            }
          }
      }
    }
  }
}
