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
		script{
                        env.RELEASE="${env.BUILD_ID}-${env.BRANCH_NAME}-${getCommitSha()}"
                }
		echo "${env.RELEASE}"
                	sh("git config --global user.email ${env.CHANGE_AUTHOR_EMAIL}")
                	sh("git config --global user.name ${env.CHANGE_AUTHOR}")
                	sh("git tag -a ${env.RELEASE} -m 'Jenkins'")
                	sh("git push --tags")
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
