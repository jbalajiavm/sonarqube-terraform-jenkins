pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID     = credentials('jenkins-aws-secret-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('jenkins-aws-secret-access-key')
    }
    stages {
        stage('Terraform Initialization') {
            steps {
                sh 'terraform init'
                sh 'pwd'
                sh 'ls -al'
                sh 'printenv'
            }
        }
        stage('SonarQube Analysis') {
           steps {
               script {
                   def scannerHome = tool 'sonarqube-1';
                   withSonarQubeEnv('sonarqube-1') {
                       sh "${scannerHome}/bin/sonar-scanner"
                   }
               }
           }
        }
        stage('Terraform Format') {
            steps {
                sh 'terraform fmt -check'
            }
        }
        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }
        stage('Terraform Planning') {
            steps {
                sh 'terraform plan -no-color'
            }
        }
        stage('Publish Artifacts') {
            steps {
                archiveArtifacts artifacts: 'terraform.tfstate', excludes: 'output/*.md'
            }
        }
        stage('Terraform Apply') {
            steps {
                sh 'terraform apply -auto-approve'
            }
        }
        stage('Terraform Destroy') {
            steps {
                sh 'terraform destroy -auto-approve'
            }
        }
    }
}
