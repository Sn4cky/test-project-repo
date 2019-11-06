pipeline {
    agent any
	
	parameters {
		choice(name: "ENVIRONMENT", choices: ["dev", "hotfix"], description: "Környezet")
	}
	
	environment {
		PROD_VERSION = "4.13.5-SNAPSHOT"
		PROJ_VERSION = "4.13.aquashop.1.1"
		JOB_VERSION = getJobVersion()
		IS_SNAPSHOT = "false"
	}
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			when {
				expression { env.IS_SNAPSHOT == "true" }
			}
			steps {
                script {
                    build job: "build-product", propagate: true, wait: true
					currentBuild.rawBuild.project.setDisplayName("aquashop-${params.DEPLOY_ENV}: ${env.PROJ_VERSION} (${env.PROD_VERSION}}")
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
                sh "echo building project"
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

def getJobVersion() {
	def job_version_split = env.PROD_VERSION.split(".")
	return "${job_version_split[0]}.${job_version_split[1]}"
}

def checkSnapshot() {
	def job_snapshot_split = env.PROD_VERSION.split("-")
	if (job_snapshot_split.size() == 2 && job_snapshot_split[1] == "SNAPSHOT") {
		echo "is snapshot"
		return "true"
	} else {
		return "false"
	}
}
