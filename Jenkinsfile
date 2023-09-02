
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
        stage("build") { // 'S' in 'Stage' should be lowercase
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
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
    }
}
