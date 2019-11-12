pipeline {
    agent any
	environment {
		//GRADLE_PROPERTIES = readProperties file: 'gradle.properties'
		//PROD_VERSION = "${env.GRADLE_PROPERTIES['smartErpVersion']}"
		//PROJ_VERSION = "${env.GRADLE_PROPERTIES['projectVersion']}"
		PROD_VERSION = "4.13.5-SNAPSHOT"
		PROJ_VERSION = "4.13.aquashop.1.2"
	}
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			steps {
                script {
					build job: "build-product", propagate: true, wait: true
					currentBuild.rawBuild.project.setDisplayName("aquashop-dev: ${env.PROJ_VERSION} (${env.PROD_VERSION})")
				}
            }
        }
        stage("build-project") {        //  Ha a projektben sincs változás, akkor elég csak deployolni, így nem kell külön deploy job sem
			when {
                anyOf {
                    changeset "**/Jenkinsfile"
					changeset "**/gradle.properties"
                    changeset "**/src/main/**"
                }
            }
            steps {
                build job: "build-project", propagate: true, wait: true
            }
        }
        stage("deploy") {               //  Utolsó lépésként deployolunk
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

//String getMainVersion() {
	//def prodVerSplit = env.PROD_VERSION.split("\\.")
	//return "${prodVerSplit[0]}.${prodVerSplit[1]}"
//}

//String isSnapshot() {
	//def prodVerSplit = env.PROD_VERSION.split("-")
	//if (prodVerSplit.size() == 2 && prodVerSplit[1] == "SNAPSHOT") {
		//return "true"
	//} else {
		//return "false"
	//}
//}
