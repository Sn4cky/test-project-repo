pipeline {
    agent any
    
    environment {
        PROD_JOB = "build-product"
        PROJ_JOB = "build-project"
    }
    
    stages {
        stage("build-product") {        //  Termék build, saját pipeline job-ot hívunk, hogy megnézzük van-e szükség build-re
            steps {
                script {
                    build job: $PROD_VER, propagate: true, wait: true
                    currentBuild.rawBuild.project.setDisplayName("pipeline-demo-project")
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
                build job: $PROJ_JOB, propagate: true, wait: true
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
