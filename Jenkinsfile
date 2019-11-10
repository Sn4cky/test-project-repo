pipeline {
    agent any
	
	parameters {
		choice(name: "ENVIRONMENT", choices: ["dev", "hotfix"], description: "Környezet")
	}
	
	environment {
		PROD_VERSION = "4.13.5-SNAPSHOT"
		PROJ_VERSION = "4.13.aquashop.1.1"
	}
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			when {
				expression { isSnapshot() == "true" }
			}
			steps {
                script {
					build job: "smart-erp-${env.PROD_VERSION.split(".")[0]}.${env.PROD_VERSION.split(".")[1]}", propagate: true, wait: true
					currentBuild.rawBuild.project.setDisplayName("aquashop-${params.ENVIRONMENT}: ${env.PROJ_VERSION} (${env.PROD_VERSION}}")
				}
            }
        }
        stage("build-project") {        //  Ha a projektben sincs változás, akkor elég csak deployolni, így nem kell külön deploy job sem
            when {
                anyOf {
                    changeset "**/Jenkinsfile"
                    changeset "/src/main/**"
                }
            }
            steps {
				sh "echo building project ${getMainVersion()}"
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

String getMainVersion() {
	def prodVerSplit = env.PROD_VERSION.split(".")
	return "${prodVerSplit[0]}.${prodVerSplit[1]}"
}

String isSnapshot() {
	def prodVerSplit = env.PROD_VERSION.split("-")
	if (prodVerSplit.size() == 2 && prodVerSplit[1] == "SNAPSHOT") {
		return "true"
	} else {
		return "false"
	}
}
