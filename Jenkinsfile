pipeline {
    agent any
    
    environment {
        AZURE_CREDENTIALS = credentials('lili-acr-credentials-id')
        RESOURCE_GROUP_NAME = 'lilirg'
        STORAGE_ACCOUNT_NAME = 'lilisa'
        CONTAINER_NAME = 'lilict'
        azure_region        = "eastus"
    }

    stages {
        stage('Authenticate with Azure') {
            steps {
                script { 
               //     withCredentials([azureServicePrincipal(${AZURE_CREDENTIALS})]) {
                    azureLogin = azureAzureCLI(
                        azureCredentialsId: ${AZURE_CREDENTIALS},
                        scriptBlock: '''
                            az login
                        '''
                    )
                    
             //       azureLogin(credentialsId: 'lili-acr-credentials-id')
                }
            }
        }
      
        stage('Create Resource Group and Storage Account') {
            steps {
                script {
                    // Create resource group if not exists
                    sh "az group create --name ${RESOURCE_GROUP_NAME} --location $azure_region --output none"
                    
                    // Check if the storage account already exists
                    def storageAccountExists = sh(script: "az storage account check-name --name ${STORAGE_ACCOUNT_NAME} --output json", returnStdout: true).trim()
                    
                    // Create storage account if it doesn't exist
                    if (storageAccountExists.contains('"nameAvailable": true')) {
                        sh "az storage account create --resource-group ${RESOURCE_GROUP_NAME} --name ${STORAGE_ACCOUNT_NAME} --sku Standard_LRS --encryption-services blob"
                    } else {
                        echo "Storage account ${STORAGE_ACCOUNT_NAME} already exists."
                    }
                }
            }
        }
      
        stage('Create Blob Container') {
            steps {
                script {
                    // Create blob container
                    sh "az storage container create --name ${CONTAINER_NAME}"
                }
            }
        }
        
      }
}
