pipeline {
    agent any 
    
    stages {
        
        stage("build voting") {
            steps {
                echo 'building vote app container'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    dir ('./vote') {
                        sh 'docker build -t solexb/voting-app:1.1 .'
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push solexb/voting-app:1.1"
                    }  
                }
                
            }
        }
         stage("build worker") {
            steps {
                echo 'building worker app container'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    dir ('./worker') {
                        sh 'docker build -t solexb/worker-app:1.1 .'
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push solexb/worker-app:1.1"
                    }  
                } 
            }
        }

        stage("build result") {
            steps {
                echo 'building result app container'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    dir ('./result') {
                        sh 'docker build -t solexb/result-app:1.1 .'
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push solexb/result-app:1.1"
                    }  
                }
            }
        }
        stage("deploy to dev env") {
            /*input {
                message "Deploy to dev env?"
                ok "Done"
            }
            steps {
               echo 'deploy to dev environment'
               sshagent(['dev-server']) {
                   sh "scp -o StrictHostKeyChecking=no docker-compose.yaml azureuser@20.228.223.50:/home/azureuser"
                   sh 'ssh -o StrictHostKeyChecking=no azureuser@20.228.223.50 "docker-compose -f docker-compose.yaml up --detach"'
                }
            }*/
            steps {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s-config', namespace: '', serverUrl: '') {
                   sh 'cd ./k8s-manifest && kubectl apply -f .'
                }
            } 
        }
    }
}
