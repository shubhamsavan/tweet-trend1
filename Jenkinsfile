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
        stage("build") {
            steps {
                sh 'mvn clean deploy'
            }
        }
        stage('SonarQube analysis') {
            environment {
                scannerHome = tool 'valaxy-sonar-scanner'
            }
            steps {
                withSonarQubeEnv('valaxy-sonarqube-server') {
                    script {
                        def scannerCmd = "${scannerHome}/bin/sonar-scanner"
                        def projectKey = "valaxy1_twittertrend"
                        def projectName = "valaxy01"
                        def organization = "valaxy1"
                        sh "${scannerCmd} -Dsonar.projectKey=${projectKey} -Dsonar.projectName='${projectName}' -Dsonar.organization=${organization}"
                    }
                }
            }
        }
    }
}

