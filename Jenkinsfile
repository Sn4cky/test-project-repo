def GRADLE_PROPERTIES = readProperties file: 'gradle.properties'

pipeline {
    agent none
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			agent none
			when {
				expression { GRADLE_PROPERTIES['smartErpVersion'].split("-").size() == 2 }
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
					def gradleProperties = readProperties file: 'gradle.properties'
					def mainVersionSplit = gradleProperties['smartErpVersion'].split('\\.')
					echo "building project"
					currentBuild.rawBuild.project.setDisplayName("aquashop-dev: ${mainVersionSplit[0]}.${mainVersionSplit[1]} (${gradleProperties['projectVersion']})")
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
