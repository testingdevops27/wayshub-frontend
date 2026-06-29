def branch = "main"
def remote = "origin"
def directory = "~/app/wayshub-frontend"
def server = "ademulyana@103.31.38.220"
def cred = "ssh-key-ademulyana"
def imageName = "oneslicedbread/wayshub-frontend:staging"

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
                    docker build -t ${imageName} .
                    exit
                    EOF"""
                }
            }
        }
        stage('docker push'){
            steps{
                sshagent([cred]){
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]){
                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${imageName}
                        exit
                        EOF"""
                    }
                }
            }
        }
        stage('docker deploy'){
            steps{
                sshagent([cred]){
                    sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                    docker stop wayshub-frontend-staging || true
                    docker rm wayshub-frontend-staging || true
                    docker run -d -p 3100:3000 --name wayshub-frontend-staging ${imageName}
                    exit
                    EOF"""
                }
            }
        }
    }
}
