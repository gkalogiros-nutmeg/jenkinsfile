pipeline {

    agent {
        label 'master'
    }

    environment{
        MAJOR_MINOR_VERSION="0.0."
    }

    stages {
        stage('Build') {
            steps {
                script {
                    if (env.BRANCH_NAME=="master"){
                        env.RELEASE="${env.MAJOR_MINOR_VERSION}${env.BUILD_ID}"
                    } else {
                        env.RELEASE="${env.BUILD_ID}-${env.BRANCH_NAME}-${getCommitSha}"
                    }
                }
                step(
                    [
                        $class: 'GitHubCommitStatusSetter',
                        statusResultSource: [
                            $class: 'ConditionalStatusResultSource',
                            results: [[$class: 'AnyBuildResult', message: 'Building on Jenkins CI', state: 'PENDING']]
                        ]
                    ]
                )
            }
        }
    }

    post {
        failure {
            step([
                $class: 'GitHubCommitStatusSetter',
                errorHandlers: [[$class: 'ShallowAnyErrorHandler']],
                statusResultSource: [
                    $class: 'ConditionalStatusResultSource',
                    results: [
                        [$class: 'AnyBuildResult', state: 'FAILURE', message: 'Build failed']
                    ]
                ]
            ])
        }

        success {
             step([
                 $class: 'GitHubCommitStatusSetter',
                 errorHandlers: [[$class: 'ShallowAnyErrorHandler']],
                 statusResultSource: [
                     $class: 'ConditionalStatusResultSource',
                     results: [
                         [$class: 'AnyBuildResult', state: 'SUCCESS', message: 'Build successful']
                     ]
                 ]
             ])
        }
    }
}

def getCommitSha() {
  sh "git rev-parse HEAD > .git/current-commit"
  return readFile(".git/current-commit").trim()
}
