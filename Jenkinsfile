pipeline{
    agent {
        label 'AGENT-1'
    }
    environment{
        project = 'expense'
        appVersion = '' // value to be passed by jenkins ci pipeline
        environment = ''
        component = 'backend'
        account_id = ''
    }
    parameters {        
        string (name: appversion, defaultValue: '')
        choice (name: ENV, choices: ["prod","dev"], description: 'Pick something')
    }

    stages {

        stage ('setup environment') {
             script{
                    appVersion = params.appversion
                    environment = params.ENV
                    account_id = pipelineGlobals.getAccountID(environment)
                }
        }
        
        stage ('deploy_dev') {
            when {
                expression { params.ENV == 'dev' }
            }

            steps {
                withAWS(region: 'us-east-1', credentials: 'aws-cred') {
                    sh """
                    aws eks update-kubeconfig --region us-east-1 --name ${project}-${environment}
                    cd helm
                    sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${environment}.yaml
                    helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                    """
                }
                // i - replace in IMAGE_VERSION value with $appversion value , target file is values-dev.yaml          
            }
        }

        stage ('deploy_prod') {
            when {
                expression { params.ENV == 'prod' }
            }

            steps {
                withAWS(region: 'us-east-1', credentials: 'aws-cred') {
                    sh """
                    aws eks update-kubeconfig --region us-east-1 --name ${project}-${environment}
                    cd helm
                    sed -i 's/IMAGE_VERSION/${appVersion}/g' values-${environment}.yaml
                    helm upgrade --install ${component} -n ${project} -f values-${environment}.yaml .
                    """
                }
                // i - replace in IMAGE_VERSION value with $appversion value , target file is values-dev.yaml          
            }
        }
    }
    posts {
        always {
            echo 'deleting the folder'
            deleteDir()
        }

        success {
            echo 'pipeline is successful'
        }
        failure {
            echo 'pipeline has failed'
        }
    }

}