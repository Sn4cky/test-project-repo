pipeline {
    agent none
	//environment {
		//GRADLE_PROPERTIES = readProperties file: 'gradle.properties'
		//PROD_VERSION = "${env.GRADLE_PROPERTIES['smartErpVersion']}"
		//PROJ_VERSION = "${env.GRADLE_PROPERTIES['projectVersion']}"
	//}
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			agent none
			
			environment {
				GRADLE_PROPERTIES = readProperties file: 'gradle.properties'
				PROD_VERSION = "${env.GRADLE_PROPERTIES['smartErpVersion']}"
				PROJ_VERSION = "${env.GRADLE_PROPERTIES['projectVersion']}"
			}
			
			when {
				expression { isSnapshot() == "true" }
			}
			
			steps {
                script {
					build job: "build-product", propagate: true, wait: true
				}
            }
        }
        stage("build-project") {        //  Ha a projektben sincs változás, akkor elég csak deployolni, így nem kell külön deploy job sem
			agent any
			when {
                anyOf {
                    changeset "**/Jenkinsfile"
					changeset "**/gradle.properties"
                    changeset "**/src/main/**"
                }
            }
            steps {
                build job: "build-project", propagate: true, wait: true
				currentBuild.rawBuild.project.setDisplayName("aquashop-dev: ${env.PROJ_VERSION} (${env.PROD_VERSION})")
            }
        }
        stage("deploy") {               //  Utolsó lépésként deployolunk
            agent any
			steps {
                script {
                    sh (
                        script: "echo deploying",
                        returnStdout: true
                    ).trim()
                }
            }
        }
    }
}

String isSnapshot() {
	def prodVerSplit = env.PROD_VERSION.split("-")
	if (prodVerSplit.size() == 2 && prodVerSplit[1] == "SNAPSHOT") {
		return "true"
	} else {
		return "false"
	}
}
