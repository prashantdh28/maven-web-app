pipeline {
    agent any
    stages{
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
                                "pattern": "target/*.war",
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
                    sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.233.119.205:/home/ubuntu/tomcat/webapps'
                }
            }
        }
    }
}
