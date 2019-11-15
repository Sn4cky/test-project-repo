def MAIN_VERSION = null
def PROD_VERSION = null
def PROJ_VERSION = null
def IS_SNAPSHOT = "false"

node {
	def gradleProperties = readProperties file: 'gradle.properties'
	def mainVerSplit = gradleProperties['smartErpVersion'].split("\\.")
	def snapshotSplit = gradleProperties['smartErpVersion'].split("-")
	
	if (snapshotSplit.size() == 2 && snapshotSplit[1] == "SNAPSHOT") {
		IS_SNAPSHOT = "true"
	}
	
	MAIN_VERSION = "${mainVerSplit[0]}.${mainVerSplit[1]}"
	PROD_VERSION = "${gradleProperties['smartErpVersion']}"
	PROJ_VERSION = "${gradleProperties['projectVersion']}"
}

pipeline {
    agent none
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			agent none
			when {
				expression { IS_SNAPSHOT == "true" }
			}
			steps {
                script {
					build job: 'build-product', propagate: true, wait: true
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
				script {
					echo "building project ${MAIN_VERSION}"
					currentBuild.rawBuild.project.setDisplayName("aquashop-dev: ${PROJ_VERSION} (${PROD_VERSION})")
				}
            }
        }
        stage("deploy") {               //  Utolsó lépésként deployolunk
            agent any
			steps {
                script {
					echo "deploying"
                }
            }
        }
    }
}
