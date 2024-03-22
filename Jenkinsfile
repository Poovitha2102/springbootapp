pipeline {
    tools {
        maven "Maven3"
    }
    agent any
        environment {
            SCANNER_HOME= tool 'sonar-scanner'
        }
    
        stages {
            stage ('Checkout from git'){
                steps {
                    git branch: 'main', url: 'https://github.com/Poovitha2102/springbootapp.git'
            }
        }
        stage ('Maven Build'){
                steps {
                   sh 'mvn clean install'
            } 
        }
        stage ('Unit Test'){
            steps {
                echo '<----------------------Unit Test Under Progess------------------>'
                sh 'mvn surefire-report:report'
                echo '<----------------------Unit Test Done------------------>'
            }
        }
        stage ('Sonarqube analysis'){
            steps {
             script {
                   withSonarQubeEnv('sonar-server') {
                       sh  ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=springbootapp \
                       -Dsonar.projectKey=springbootapp '''
                    }
                }
            }
        }
        stage ('Quality Gate Test'){
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                }
            }
        }
        stage ('Build Docker Image'){
           steps {
                script {
                    sh 'docker build -t myrepo .'
                }
            }
        } 
        
        
    stage('Kuberernetes Deployment'){
        steps{
            script {
                sh 'aws eks --region us-east-1 update-kubeconfig --name my-first-eks-cluster1'
                sh 'kubectl apply -f eks-deploy-k8s.yaml'
            }
        }
    }
        
    }
}
