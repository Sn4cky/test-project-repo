pipeline {
    agent none
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			agent none
			steps {
                script {
					def gradleProperties = readProperties file: 'gradle.properties'
					def snapshotSplit = gradleProperties['smartErpVersion'].split('-')
					
					if (snapshotSplit.size() == 2 && snapshotSplit[1] == 'SNAPSHOT') {
						build job: "build-product", propagate: true, wait: true
					} else {
						echo "product version is not snapshot, skipping build"
					}
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
