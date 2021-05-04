pipeline{
    agent any
    tools {
      maven 'jenkins_maven'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github',
                    url: 'https://github.com/Safiquddin/dockeransiblejenkins'
            }
        }
        stage('MAVEN BUILD'){
            steps{
                sh "mvn clean install"
            }
        }
        stage('Docker Build'){
            steps{
                sh "docker build . -t safiquddin/safiqapp:${DOCKER_TAG}"
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'docker-hubpwd')]) {
                    sh "docker login -u safiquddin -p ${docker-hubpwd}"
                }
                sh sh "docker push safiquddin/safiqapp:${DOCKER_TAG} "
            }
        }
        
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'ansible', disableHostKeyChecking: true, extras: 'DOCKER_TAG=""', installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}
def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
}
