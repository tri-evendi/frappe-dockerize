// create pipeline for cloning the repo and building the image then push it to docker hub
pipeline {
    agent any
    stages {
        stage('Sync Repository') {
            // when {
            //     expression { 
            //         return env.BRANCH_NAME == 'origin/main'
            //     }
            // }
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
            // when {
            //     expression { 
            //         return env.BRANCH_NAME == 'origin/main'
            //     }
            // }
            steps {
                echo 'Building image...'
                // using credentials dockerhub
                withDockerRegistry([credentialsId: 'Docker_Vendy', url: '']) {
                    sh '''
                        cd /root/traefik/apps/frappe-dockerize &&\
                        docker build -t evendyx/frappe-dockerize:latest . &&\
                        docker tag frappe-dockerize:latest evendyx/frappe-dockerize:latest &&\
                        docker push evendyx/frappe-dockerize:latest
                    '''
                }
            }
        }
    }
}