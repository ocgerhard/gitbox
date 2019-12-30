pipeline {
    agent { label 'docker' }

    options {
        buildDiscarder(logRotator(numToKeepStr: '1', daysToKeepStr: '1'))
        ansiColor('xterm')
        timestamps()
        timeout(time: 2, unit: 'HOURS')
    }

    triggers {
        pollSCM 'H/2 * * * *'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building...'
                }
            }
        }
   
        stage('publish') {
	steps { 

		withDockerRegistry([url: 'https://ocnexus.opencollab.co.za/', credentialsId: '06066572-c607-4f28-a4d6-adc78913ca6d']) {
		sh "docker build --no-cache --force-rm -t ocnexus.opencollab.co.za/repository/oc-docker/gitbox:latest ${WORKSPACE}"
		sh "docker tag ocnexus.opencollab.co.za/repository/oc-docker/gitbox:latest ocnexus.opencollab.co.za/repository/oc-docker/gitbox:b${env.BUILD_NUMBER}"
		// Push
		sh "docker push ocnexus.opencollab.co.za/repository/oc-docker/gitbox:latest"
		sh "docker push ocnexus.opencollab.co.za/repository/oc-docker/gitbox:b${env.BUILD_NUMBER}"
		// Cleanup
		sh "docker rmi ocnexus.opencollab.co.za/repository/oc-docker/gitbox:latest"
		sh "docker rmi ocnexus.opencollab.co.za/repository/oc-docker/gitbox:b${env.BUILD_NUMBER}"
		}

	      }

	}
   }

    post {

        failure {


            emailext([subject           : '$DEFAULT_SUBJECT',
                      body              : '$DEFAULT_CONTENT',
                      recipientProviders: [
                              [$class: 'CulpritsRecipientProvider'],
                              [$class: 'DevelopersRecipientProvider'],
                              [$class: 'RequesterRecipientProvider']
                      ],
                      replyTo           : '$DEFAULT_REPLYTO',
                      to                : '$DEFAULT_RECIPIENTS'])
        }

        changed {
            emailext([subject           : '$DEFAULT_SUBJECT',
                      body              : '$DEFAULT_CONTENT',
                      recipientProviders: [
                              [$class: 'CulpritsRecipientProvider'],
                              [$class: 'DevelopersRecipientProvider'],
                              [$class: 'RequesterRecipientProvider']
                      ],
                      replyTo           : '$DEFAULT_REPLYTO',
                      to                : '$DEFAULT_RECIPIENTS'])
        }
    }
}
