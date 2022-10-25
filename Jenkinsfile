pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    parameters {
        string(name: 'VERSION', defaultVaule: '', description: "Version to Build")
    }
    stages {
        stage ("build image") {
            steps {
                script {
                    echo "Building the docker image"
                    withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'PASS', usernameVariable: "USER")]) {
                        sh "docker build -t nikolozjakhua/my-app:${VERSION} ."
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push nikolozjakhua/my-app:${VERSION}"
                    }
                }
            }
        }
        stage ("Deploying") {
            steps {
                script {
                    def dockerCmd = "docker run -d -p 3000:3080 nikolozjakhua/my-app:${VERSION}"
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=0 ec2-user@3.73.38.27 ${dockerCmd}"
                    }           
                }
            }
        }
    }
}
