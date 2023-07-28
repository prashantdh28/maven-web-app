pipeline {
    agent any

    stages {
        stage('Git Clone') {
            steps {
                git credentialsId: 'aws-cred', url: 'https://github.com/prashantdh28/https-github.com-MithunTechnologiesDevOps-maven-web-application.git'
            }
        }

        stage('Maven Build') {
            steps {
                script {
                    def mavenHome = tool name: "maven", type: "maven"
                    def mavenCMD = "${mavenHome}/bin/mvn"
                    sh "${mavenCMD} clean package"
                }
            }
        }

        stage('Store War to JFrog Artifactory') {
            steps {
                script {
                    def server = Artifactory.server 'Artifactory'
                    def uploadSpec = """{
                        "files": [
                            {
                                "pattern": "target/maven-web-application.war",
                                "target": "java-web-app/com.mt/maven-web-application/0.0.1-SNAPSHOT/"

                            }
                        ]
                    }"""
                    server.upload spec: uploadSpec
                }
            }
        }

        stage('Deploy') {
            steps {
                sshagent(['tomcat-server']) {
                    sh 'scp -o StrictHostKeyChecking=no target/maven-web-application.war ubuntu@65.0.180.121:/home/ubuntu/tomcat/webapps'
                }
            }
        }
    }
}
