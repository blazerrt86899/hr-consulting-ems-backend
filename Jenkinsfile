pipeline {
    
    agent any
    tools {
        maven "mvn3"
    }
    stages {
        stage('pull from scm'){
            steps {
                git branch: 'main', credentialsId: 'git-lab-token', url: 'https://github.com/blazerrt86899/hr-consulting-ems-backend.git'
            }
        }
        
        stage('mvn build') {
            steps {
                sh "mvn clean install"
            }
            post {
                success {
                    junit '**/target/surefire-reports/*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
