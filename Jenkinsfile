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
				expression { PROD_VERSION.split("-").size() == 2 && PROD_VERSION.split("-")[1] == "SNAPSHOT" }
			}
			
			steps {
                script {
					build job: "build-product", propagate: true, wait: true
				}
            }
        }
        stage("build-project") {        //  Ha a projektben sincs változás, akkor elég csak deployolni, így nem kell külön deploy job sem
			agent any
			
			environment {
				GRADLE_PROPERTIES = readProperties file: 'gradle.properties'
				PROD_VERSION = "${env.GRADLE_PROPERTIES['smartErpVersion']}"
				PROJ_VERSION = "${env.GRADLE_PROPERTIES['projectVersion']}"
			}
			
			when {
                anyOf {
                    changeset "**/Jenkinsfile"
					changeset "**/gradle.properties"
                    changeset "**/src/main/**"
                }
            }
			
            steps {
				script {
					build job: "build-project", propagate: true, wait: true
					currentBuild.rawBuild.project.setDisplayName("aquashop-dev: ${env.PROJ_VERSION} (${env.PROD_VERSION})")
				}
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
