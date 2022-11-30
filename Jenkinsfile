// create pipeline for cloning the repo and building the image then push it to docker hub
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Docker_Vendy')
    }
    stages {
        stage('Sync Repository') {
            steps {
                echo 'Cloning repo...'
                sshagent(credentials: ['Server_TAN_RnD']) {
                    sh '[ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh'
                    sh 'ssh-keyscan -t rsa,dsa 139.162.18.93 >> ~/.ssh/known_hosts'
                    sh '''
                        ssh -o StrictHostKeyChecking=no root@139.162.18.93 "\
                        cd /root/traefik/apps/frappe-dockerize &&\
                        git branch &&\
                        git pull git@github.com:tri-evendi/frappe-dockerize.git"
                    '''
                }
            }
        }
        stage('Build Image and Push to Docker Hub') {
            steps {
                echo 'Building image...'
                sshagent(credentials: ['Server_TAN_RnD']) {
                    sh '[ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh'
                    sh 'ssh-keyscan -t rsa,dsa 139.162.18.93 >> ~/.ssh/known_hosts'
                    sh '''
                        ssh -o StrictHostKeyChecking=no root@139.162.18.93 "\
                        cd /root/traefik/apps/frappe-dockerize &&\
                        docker build -t evendyx/frappe-dockerize:latest . &&\
                        echo $DOCKERHUB_CREDENTIALS | docker login --username $DOCKERHUB_CREDENTIALS_USR --password-stdin &&\
                        docker push evendyx/frappe-dockerize:latest"
                    '''
                }
            }
        }
    }
}