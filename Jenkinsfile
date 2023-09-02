pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.4/bin:$PATH"
    }
    stages {
        stage("Build") { // Changed 'build' to 'Build' for consistency
            steps {
                script {
                    try {
                        sh 'mvn clean deploy'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Failed to build: ${e.message}")
                    }
                }
            }
        }
        stage('SonarQube Analysis') { // Renamed for clarity and consistency
            environment {
                scannerHome = tool 'valaxy-sonar-scanner'
            }
            steps {
                script {
                    def scannerCmd = "${scannerHome}/bin/sonar-scanner"
                    def projectKey = "valaxy1_twittertrend"
                    def projectName = "valaxy01"
                    def organization = "valaxy1"
                    
                    try {
                        sh "${scannerCmd} -Dsonar.projectKey=${projectKey} -Dsonar.projectName='${projectName}' -Dsonar.organization=${organization}"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("SonarQube analysis failed: ${e.message}")
                    }
                }
            }
        }
    }
}

