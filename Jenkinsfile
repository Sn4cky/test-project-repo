pipeline {
    agent any
	
	parameters {
		choice(name: "ENVIRONMENT", choices: ["dev", "hotfix"], description: "Környezet")
	}
	
	environment {
		PROD_VERSION = "4.13.5-SNAPSHOT"
		PROJ_VERSION = "4.13.aquashop.1.1"
		JOB_VERSION = ""
		IS_SNAPSHOT = "false"
	}
    
    stages {
		stage("snapshot-check") {
			steps {
				script {
					env.PROD_VERSION = "4.13.5-SNAPSHOT"
					env.PROJ_VERSION = "4.13.aquashop.1.1"
					
					def job_version_split = env.PROD_VERSION.split(".")
					env.JOB_VERSION = "$job_version_split[0].$job_version_split[1]"
					
					def job_snapshot_split = env.PROD_VERSION.split("-")
					for (i in job_snapshot_split) {
						echo i
					}
					if (job_snapshot_split.size() == 2 && job_snapshot_split[1] == "SNAPSHOT") {
						env.IS_SNAPSHOT = "true"
					}
				}
			}
		}
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
