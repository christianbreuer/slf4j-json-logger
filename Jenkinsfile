
pipeline {
	agent any
	stages {
		stage("Checkout") {
			steps {
				checkout scm
				script { currentBuild.result = 'SUCCESS'}
			}
		}
		stage("Build & Deploy") {
			tools {
				jdk "jdk"
				maven "maven"
			}
			steps { sh "mvn clean install" }
			post {
				failure {
					script { currentBuild.result = 'FAILED'}
				}
			}
		}
		
		
		stage("Publish Reports") {
			steps {
				// Reihenfole hier bestimmt die Reihenfolge der Reports in Jenkins
				junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml'
				//step( [ $class: 'JacocoPublisher' ] )
				//findbugs defaultEncoding: '', excludePattern: '', healthy: '', includePattern: '', pattern: 'target/findbugsXml.xml', unHealthy: ''
				//checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
				//dry canComputeNew: false, defaultEncoding: '', healthy: '', normalThreshold: 20, pattern: '', unHealthy: ''			
			}
			post {
				failure {
					script { currentBuild.result = 'FAILED'}
				}
			}
		}
		
	}
	post {
		always {
			step([$class: 'Mailer', notifyEveryUnstableBuild: true, recipients: emailextrecipients([
					[$class: 'CulpritsRecipientProvider'],
					[$class: 'RequesterRecipientProvider']
				])])
		}
	}
}
