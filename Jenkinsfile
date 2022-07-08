pipeline {
    environment {
        registry = "40.121.32.216:8085/hrms"
        registryCredential = 'nexus-repo'
    }
    agent any
    tools {
        maven "mvn3"
    }
    stages {
        stage('pull from scm'){
            steps {
                git branch: 'docker', credentialsId: 'git-lab-token', url: 'https://github.com/blazerrt86899/hr-consulting-ems-backend.git'
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
        stage('checkstyle') {
            steps {
                    sh 'mvn checkstyle:checkstyle'
            }
        }
        
        stage('checkstyle report') {
            steps {
                recordIssues(tools: [checkStyle(pattern: 'target/checkstyle-result.xml')])
            }
        }
        
        stage('code coverage') {
            steps {
                jacoco()
            }
        }
        
        stage('code quality with Sonar') {
            steps {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=hrmsspring -Dsonar.host.url=http://20.231.202.95:9000/ -Dsonar.login=sqa_eb0963c26f4515d4860176b58d840a34ba6a3e49'
            }
        }
        
        stage ('Nexus upload')  {
          steps {
          nexusArtifactUploader(
          nexusVersion: 'nexus3',
          protocol: 'http',
          nexusUrl: '40.121.32.216:8081',
          groupId: 'com.example.angular',
          version: '0.0.1-SNAPSHOT',
          repository: 'maven-snapshots',
          credentialsId: 'nexus-repo',
          artifacts: [
            [artifactId: 'springboot-angular-kubernetes',
             classifier: '',
             file: 'target/springboot-angular-kubernetes-0.0.1-SNAPSHOT.jar',
             type: 'jar']
        ]
        )
          }
        }
        stage('docker image') {
            steps {
                script {
                  dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('docker push') {
            steps {
                script {
                  withDockerRegistry(credentialsId: registryCredential, url: 'http://40.121.32.216:8085') {
                      dockerImage.push()
                      
                  }
                }
            }
        }
    
    }
}
