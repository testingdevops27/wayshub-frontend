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
                    sh "ssh -o StrictHostKeyChecking=no ${server} 'cd ${directory} && git pull ${remote} ${branch}'"
                }
            }
        }
        stage('docker build'){
            steps{
                sshagent([cred]){
                    sh "ssh -o StrictHostKeyChecking=no ${server} 'cd ${directory} && docker build -t ${imageName} .'"
                }
            }
        }
        stage('docker push'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]){
                    sshagent([cred]){
                        sh '''ssh -o StrictHostKeyChecking=no ''' + server + ''' "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin && docker push ''' + imageName + '''"'''
                    }
                }
            }
        }
        stage('docker deploy'){
            steps{
                sshagent([cred]){
                    sh "ssh -o StrictHostKeyChecking=no ${server} 'docker stop wayshub-frontend-staging || true && docker rm wayshub-frontend-staging || true && docker run -d -p 3100:80 --name wayshub-frontend-staging ${imageName}'"
                }
            }
        }
    }
}
