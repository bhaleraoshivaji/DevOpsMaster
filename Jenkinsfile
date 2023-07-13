pipeline {
    agent any

    tools {
        // Install the Maven version configured as "maven_3.8.5" and add it to the path.
        maven "maven_3.8.5"
    }

    stages {
        stage('Build Maven') {
            steps {
                    // Get some code from a GitHub repository
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/bhaleraoshivaji/DevOpsMaster.git']])
                 
                    // Run Maven on a Unix agent.
                    sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        
          stage('Build Docker Image'){
            steps{
                script{
                    sh 'docker build -t bhaleraoshivaji/devops-integration .'
                }
            }
        }
            stage('Push Image to Docker Hub'){
                steps{
                    script{
                        withCredentials([string(credentialsId: 'dockerhubnewkey', variable: 'dockerhubkey')]) {
                            sh 'docker login -u bhaleraoshivaji -p ${dockerhubkey}'
                    }
                       sh 'docker push bhaleraoshivaji/devops-integration'
                }
            }
        }
        
        stage('Deploy Image to Remote Server'){
            steps{
               ansiblePlaybook credentialsId: 'sshprivatekey', installation: 'ansible', disableHostKeyChecking: true, inventory: 'inventory', playbook: 'playbook.yml'
            }
        }
    }
}
