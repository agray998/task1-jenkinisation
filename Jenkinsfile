pipeline {
    agent any
    environment {
        YOUR_NAME = credentials("YOUR_NAME")
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t agray998/task1jenk .
                docker build -t agray998/task1-nginx nginx
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push agray998/task1jenk
                docker push agray998/task1-nginx
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                ssh jenkins@adam-deploy <<EOF
                docker pull agray998/task1jenk
                docker pull agray998/task1-nginx
                export YOUR_NAME=${YOUR_NAME}
                docker stop nginx && echo "Stopped nginx" || echo "nginx is not running"
                docker rm nginx && echo "removed nginx" || echo "nginx does not exist"
                for i in {1..3}; do
                docker stop flask-app-${i} && echo "Stopped flask-app" || echo "flask-app is not running"
                done
                for i in {1..3}; do
                docker rm flask-app-${i} && echo "removed flask-app" || echo "flask-app does not exist"
                done
                docker network rm task1-net && echo "removed network" || echo "network already removed"
                docker network create task1-net
                for i in {1..3}; do
                docker run -d --name flask-app-${i} --network task1-net -e YOUR_NAME=${YOUR_NAME} agray998/task1jenk
                done
                docker run -d --name nginx --network task1-net -p 80:80 agray998/task1-nginx
                '''
            }

        }

    }

}

