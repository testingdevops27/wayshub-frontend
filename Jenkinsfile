def branch = "main"
def remote = "origin"
def directory = "~/jenkins/wayshub-frontend"
def server = "devops27@103.193.178.218"
def cred = "ssh-key-devops27"

pipeline{
    agent any
    stages{
        stage('repo pull'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory}
                    git pull ${remote} ${branch}
                    exit
                    EOF"""
                }
            }
        }
        stage('docker build'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    cd ${directory}
                    docker build -t wayshub-frontend .
                    exit
                    EOF"""
                }
            }
        }
        stage('docker run'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    docker stop wayshub_frontend || true
                    docker rm wayshub_frontend || true
                    docker run -d -p 3000:3000 --name wayshub_frontend wayshub-frontend
                    exit
                    EOF"""
                }
            }
        }
    }
}
