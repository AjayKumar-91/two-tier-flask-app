pipeline {
    agent any
    stages {
        stage("Clean Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Code Clone") {
            steps {
                deleteDir()
                git branch: 'master', url: 'https://github.com/AjayKumar-91/two-tier-flask-app'
            }
        }


        stage("Build Docker Images") {
            steps {
                sh 'docker build -t two-tier-flask-app .'
            }
        }

        stage("Test") {
            steps {
                echo "Developer/Tester is testing"
            }
        }
         stage('Push Image to DOcker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId:"DockerHub_Credentials",
                    passwordVariable: "dockerHubPass",
                    usernameVariable: "dockerHubUser"               
                )]){ 
                    sh '''
                    docker login -u $dockerHubUser -p $dockerHubPass
                    docker tag two-tier-flask-app $dockerHubUser/two-tier-flask-app
                    docker push $dockerHubUser/two-tier-flask-app
                    '''
                }
            }
        }

        stage("Deploy") {
            steps {
                sh '''
                docker stop flask-app || true
                docker rm flask-app || true
                docker rmi two-tier-flask-app || true

                docker compose down || true
                docker compose up -d --build flask-app
                '''
            }
        }
    }
}
