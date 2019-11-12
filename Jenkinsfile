pipeline {
    agent none
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
			agent none
			when {
				expression { "true" == "true" }
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
				script {
					build job: "build-project", propagate: true, wait: true
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
