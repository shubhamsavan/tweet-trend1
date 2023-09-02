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
        stage("Build") {
            steps {
                echo "----------- Build started ----------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "----------- Build completed ----------"
            }
        }
        stage("Unit Tests") {
            steps {
                echo "----------- Unit tests started ----------"
                sh 'mvn surefire-report:report'
                echo "----------- Unit tests completed ----------"
            }
        }
        stage('SonarQube Analysis') {
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
 stage("Quality Gate"){
    steps {
        script {
        timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
    def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
    if (qg.status != 'OK') {
      error "Pipeline aborted due to quality gate failure: ${qg.status}"
    }
  }
}
    }
  }
}
}

