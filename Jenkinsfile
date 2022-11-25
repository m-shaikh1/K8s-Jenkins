pipeline{
    agent any
    environment {
        DOCKERHUB_CREDENTIALS=credentials('DOCKERHUB')
    }
    stages {
        stage('Build Maven') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'm-shaikh1', url: 'https://github.com/m-shaikh1/K8s-Jenkins.git']]])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                  sh 'docker build -t mshaikh911/nodejsapp-1.0:latest .'
                }
            }
        }
        stage('Deploy Docker Image') {
            steps {
                 script {
                //  withCredentials([string(credentialsId: 'Humodi911', variable: 'dockerhubpwd')]) {
                //     sh 'docker login -u mshaikh911 -p ${dockerhubpwd}'
                //  }  
                 sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                 sh 'docker push mshaikh911/nodejsapp-1.0:latest'
                }
            }
        }
    
    stage('Deploy App on k8s') {
      steps {
            sshagent(['k8s']) {
            sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml root@172.105.49.113:/root"
            script {
                sh 'mkdir -p $HOME/.kube'
                sh 'sudo cp -i config $HOME/.kube/config'
                try{
                    sh "ssh root@172.105.49.113 kubectl apply -f ."
                }catch(error){
                    sh "ssh root@172.105.49.113 kubectl create -f ."
            }
}
        }
      
    }
    }
    }
}