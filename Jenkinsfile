pipeline {
    agent any
    parameters {
        string(name: 'VERSION', defaultValue: '', description: "Version to Build")
    }
    stages {
        stage ("build image") {
            steps {
                script {
                    echo "Building the docker image"
                    withCredentials([usernamePassword(credentialsId: 'docker-creds', passwordVariable: 'PASS', usernameVariable: "USER")]) {
                        sh "docker build -t nikolozjakhua/my-app:${params.VERSION} ."
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push nikolozjakhua/my-app:${params.VERSION}"
                    }
                }
            }
        }
        stage ("Deploying") {
            steps {
                script {
                    def dockerCmd = "docker run -d -p 3000:3080 nikolozjakhua/my-app:${params.VERSION}"
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@[IP_AWS_EC2] ${dockerCmd}"
                    }           
                }
            }
        }
    }
}
