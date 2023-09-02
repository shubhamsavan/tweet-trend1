def registry = 'https://shubham01.jfrog.io'
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
        stage("Quality Gate Check") {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }  
        stage("Jar Publish") {
           steps {
              script {
                 try {
                      echo '<--------------- Jar Publish Started --------------->'
                      def server = Artifactory.newServer(url: registry + "/artifactory", credentialsId: "artifact-cred")
                      def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"
                      def uploadSpec = """{
                              "files": [
                             {
                              "pattern": "jarstaging/*", // Update the pattern as needed
                              "target": "libs-release-local/{1}", // Update the target repository path
                              "flat": "false",
                              "props": "${properties}",
                              "exclusions": ["*.sha1", "*.md5"]

                                 ]
                }"""
                def buildInfo = server.upload(uploadSpec)
                buildInfo.env.collect()
                server.publishBuildInfo(buildInfo)
                echo '<--------------- Jar Publish Ended --------------->'
             catch (Exception e) {
                currentBuild.result = 'FAILURE'
                error "JAR publishing failed: ${e.message}"
            }
        }
    }
           }
    }
}
}
