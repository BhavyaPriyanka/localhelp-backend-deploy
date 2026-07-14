pipeline{

    agent{
        label 'AGENT-1'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }

    parameters{

        string(

            name: 'VERSION',
            defaultValue: '1.0.0',
            description:'WHAT is the app version'
        )
    }

    stages {

        stage('PRINT THE VERSION') {
            steps {
               echo "APP VERSION IS: ${params.VERSION} "
            }
        }
    }

        stage('Terraform INIT') {
                steps {
                sh """
                    cd terraform
                    terraform init
                """
                }
            }

        stage('Terraform PLAN') {
            steps {
            sh """
                cd terraform
                terraform plan -var="app_version=${params.VERSION}"
            """
            }

        // stage('Terraform DEPLOY') {
        //     steps {
        //     sh """
        //         cd terraform
        //         terraform apply -auto-approve
        //     """
        //     }
        // }
        }

}

