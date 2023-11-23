pipeline {
    agent any
    environment {
        YOUR_NAME = credentials("YOUR_NAME")
    }
    stages {
        // stage('Build') {
        //     steps {
        //         sh '''
        //         docker build -t agray998/task1jenk .
        //         '''
        //     }

        // }
        // stage('Push') {
        //     steps {
        //         sh '''
        //         docker push agray998/task1jenk
        //         '''
        //     }

        // }
        stage('Staging Deploy') {
            steps {
                sh '''
                kubectl apply -f nginx-config.yaml --namespace staging
                sed -e 's,{{YOUR_NAME}},'${YOUR_NAME}',g;' app-manifest.yaml | kubectl apply -f - --namespace staging
                kubectl apply -f nginx-pod.yaml --namespace staging
                '''
            }
        }
        stage('Quality Check') {
            steps {
                sh '''
                sleep 50
                kubectl get svc -o json | jq '.items[] | select(.metadata.name == "nginx") | .status.loadBalancer.ingress[0].ip' | tr -d '"' | read STAGING_IP
                export STAGING_IP
                pip3 install requests
                python3 test-app.py
                '''
            }
        }
        stage('Prod Deploy') {
            steps {
                sh '''
                kubectl apply -f nginx-config.yaml --namespace prod
                sed -e 's,{{YOUR_NAME}},'${YOUR_NAME}',g;' app-manifest.yaml | kubectl apply -f - --namespace prod
                kubectl apply -f nginx-pod.yaml --namespace prod
                sleep 60
                kubectl get services --namespace prod
                '''
            }

        }

    }

}

