pipeline {
    agent any
    tools {
        maven "mvn3"
    }
    stages {
        stage('pull from scm'){
            steps {
                git branch: 'aksdeploy', credentialsId: 'git-lab-token', url: 'https://github.com/blazerrt86899/hr-consulting-ems-backend.git'
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
            post {
                
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
        
        stage('Deploy to aks') {
            steps {
                script {
                        kubeconfig(credentialsId: 'k8sconfigfile', serverUrl: 'https://springaks-dns-daf3ead2.hcp.eastus.azmk8s.io:443') {
                        sh 'kubectl apply -f ./kubernetes/mysqldb-root-credentials.yml'
                        sh 'kubectl apply -f ./kubernetes/mysqldb-credentials.yml'
                        sh 'kubectl apply -f ./kubernetes/mysql-configmap.yml'
                        sh 'kubectl apply -f ./kubernetes/mysql-deployment.yml'
                        sh 'kubectl apply -f ./kubernetes/deployment.yml'
                    }
                }
            }
        }
    
    }
}
