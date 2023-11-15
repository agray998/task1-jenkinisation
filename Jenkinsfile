pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh '''
                docker build -t agray998/task1jenk .
                '''
            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push agray998/task1jenk
                '''
            }

        }
        stage('Deploy') {
            steps {
                sh '''
                docker stop task1 && echo "Stopped task1" || echo "task1 is not running"
                docker rm task1 && echo "removed task1" || echo "task1 does not exist"
                docker run -d -p 80:5500 --name task1 agray998/task1jenk
                '''
            }

        }

    }

}

