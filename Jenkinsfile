node {
    withEnv([
        'AZURE_SUBSCRIPTION_ID=b043f2a8-f797-489c-b070-3be1f3ddb04d',
        'AZURE_TENANT_ID=98b89e75-1ffc-4325-aae3-0645602da377',
        'AZURE_RESOURCE_GROUP=jenkins-get-started-rg',
        'AZURE_WEBAPP_NAME=yqin-jenkins-ex8-20261029',
        "AZURE_CONFIG_DIR=${pwd(tmp: true)}/azure-cli"
    ]) {
        stage('init') {
            checkout scm
        }

        stage('build') {
            sh 'mvn clean package'
        }

        stage('deploy') {
            withCredentials([usernamePassword(
                credentialsId: 'AzureServicePrincipal',
                passwordVariable: 'AZURE_CLIENT_SECRET',
                usernameVariable: 'AZURE_CLIENT_ID'
            )]) {
                try {
                    sh '''
                        set +x
                        az login --service-principal \
                            --username "$AZURE_CLIENT_ID" \
                            --password "$AZURE_CLIENT_SECRET" \
                            --tenant "$AZURE_TENANT_ID" \
                            --output none
                        az account set --subscription "$AZURE_SUBSCRIPTION_ID"
                        az webapp deploy \
                            --resource-group "$AZURE_RESOURCE_GROUP" \
                            --name "$AZURE_WEBAPP_NAME" \
                            --src-path target/calculator-1.0.war \
                            --type war
                    '''
                } finally {
                    sh 'az logout || true'
                }
            }
        }
    }
}
