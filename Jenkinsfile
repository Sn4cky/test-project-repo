pipeline {
    agent any
	
	parameters {
		string(name: "JOB_NAME", defaultValue: "", description: "A job neve (ha üres, akkor marad az eddigi)")
		choice(name: "ENVIRONMENT", choices: ["dev", "hotfix"], description: "Környezet")
	}
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
            steps {
                script {
                    build job: "build-product", propagate: true, wait: true
                }
            }
        }
		stage("rename-job") {
			when {
				expression { ${params.JOB_NAME} != "" }
			}
			steps {
				script {
					currentBuild.rawBuild.project.setDisplayName("${params.JOB_NAME}")
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
