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
		nexusPublisher nexusInstanceId: 'nexus3', nexusRepositoryId: 'v2'
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
