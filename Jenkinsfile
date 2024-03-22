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
        
        stage ("Push Image to ECR")  {
        steps {
            script {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 526647897118.dkr.ecr.us-east-1.amazonaws.com'
                //sh 'docker build -t springboot-app .'
                sh 'docker tag springboot-app:latest 526647897118.dkr.ecr.us-east-1.amazonaws.com/springboot-app:latest'
                sh 'docker push 526647897118.dkr.ecr.us-east-1.amazonaws.com/springboot-app:latest'

            }
        }
    }
        
    }
}
