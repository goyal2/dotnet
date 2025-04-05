// For terraform
pipeline {
    agent any
    
    environment {
        ARM_CLIENT_ID       = credentials('azure-client-id')
        ARM_CLIENT_SECRET   = credentials('azure-client-secret')
        ARM_SUBSCRIPTION_ID = credentials('azure-subscription-id')
        ARM_TENANT_ID       = credentials('azure-tenant-id')
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/goyal2/.Net-project', branch: 'main'
            }
        }

        stage('Terraform Init') {
            steps {
                bat 'terraform init'
            }
        }

        stage('Terraform Plan') {
            steps {
                bat '''
                    terraform plan ^
                      -var client_id=%ARM_CLIENT_ID% ^
                      -var client_secret=%ARM_CLIENT_SECRET% ^
                      -var tenant_id=%ARM_TENANT_ID% ^
                      -var subscription_id=%ARM_SUBSCRIPTION_ID%
                    '''
            }
        }

        stage('Terraform Apply') {
            steps {
                bat '''
                terraform apply -auto-approve ^
                  -var client_id=%ARM_CLIENT_ID% ^
                  -var client_secret=%ARM_CLIENT_SECRET% ^
                  -var tenant_id=%ARM_TENANT_ID% ^
                  -var subscription_id=%ARM_SUBSCRIPTION_ID%
                '''
            }
        }
         stage('Build .NET App') {
            steps {
                dir('WebApiJenkins') { // Adjust to your .NET project folder
                    bat 'dotnet publish -c Release -o publish'
                }
            }
        }

        stage('Deploy to Azure') {
            steps {
                bat '''
                powershell Compress-Archive -Path ProductService\\publish\\* -DestinationPath publish.zip -Force
                az webapp deployment source config-zip --resource-group jenkins-somya-rg --name "jenkins-somya-app123" --src "publish.zip"

                '''
            }
        }   
    }
}
