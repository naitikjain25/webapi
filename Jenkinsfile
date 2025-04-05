pipeline {
     agent any

     // environment {
     //     ARM_CLIENT_ID = credentials('AZURE_CREDENTIALS_CLIENT_ID')
     //     ARM_CLIENT_SECRET = credentials('AZURE_CREDENTIALS_CLIENT_SECRET')
     //     ARM_SUBSCRIPTION_ID = credentials('AZURE_CREDENTIALS_SUBSCRIPTION_ID')
     //     ARM_TENANT_ID = credentials('AZURE_CREDENTIALS_TENANT_ID')
     // }
     environment {
        AZURE_CREDENTIALS_ID = 'azure-service-principal'
        RESOURCE_GROUP = 'rg-jenkins'
        APP_SERVICE_NAME = 'webapijenkinsnaitik457'
    }

  
 stages {
      stage('Checkout Code') {
            steps {
                git branch: 'master', url: 'https://github.com/samriddhagarwal07/WebApplication6.git'
            }
        }

      stage('Terraform Init') {
           steps {
             dir('jenkins_assignment3') {
                 bat 'terraform init'
             }
           }
      }
   
      stage('Terraform Plan & Apply') {
         steps {
             dir('jenkins_assignment3') {
               bat 'terraform plan -out=tfplan'
               bat 'terraform apply -auto-approve tfplan'
             }
         }
      }
   
       stage('Build') {
            steps {
                bat 'dotnet restore'
                bat 'dotnet build --configuration Release'
                bat 'dotnet publish -c Release -o ./publish'
            }
        }

   
       // stage('Deploy to Azure App Service') {
       //     steps {
       //         bat 'az login --service-principal -u %ARM_CLIENT_ID% -p %ARM_CLIENT_SECRET% --tenant %ARM_TENANT_ID%'
       //         bat 'az account set --subscription %ARM_SUBSCRIPTION_ID%'
       //         bat 'az webapp deploy --resource-group rg-jenkins --name webapijenkinsnaitik457 --src-path webapi\\out --type zip'
       //      }
       // }
      stage('Deploy') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
                    bat "az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET --tenant $AZURE_TENANT_ID"
                    bat "powershell Compress-Archive -Path ./publish/* -DestinationPath ./publish.zip -Force"
                    bat "az webapp deploy --resource-group $RESOURCE_GROUP --name $APP_SERVICE_NAME --src-path ./publish.zip --type zip"
                }
            }
        }

      
 }

     post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }

}
