pipeline {
    agent {
        label 'AGENT-1'
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    parameters {
        string(
            name: 'VERSION',
            defaultValue: '1.0.0',
            description: 'Application version'
        )
    }

    environment {
        APP_URL = 'http://backend-dev.localhelp.store'
        ZAP_URL = 'http://zap.localhelp.store:8080'
    }

    stages {
        stage('Environment Check') {
            steps {
                sh '''
                    hostname
                    pwd
                    whoami
                    terraform version
                '''
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Print Version') {
            steps {
                echo "Application Version : ${params.VERSION}"
            }
        }

        stage('Terraform INIT') {
            steps {
                sh '''
                    cd terraform
                    terraform init
                '''
            }
        }

        stage('Terraform Format') {
            steps {
                sh '''
                    cd terraform
                    terraform fmt -check
                '''
            }
        }

        stage('Terraform PLAN') {
            steps {
                sh """
                    cd terraform

                    terraform plan \
                    -var="app_version=${params.VERSION}"
                """
            }
        }

        stage('Terraform DEPLOY') {
            steps {
                sh """

                    cd terraform

                    terraform apply \
                    -auto-approve \
                    -var="app_version=${params.VERSION}"

                """
            }
        }

        stage('Wait For Application') {
            steps {
                sh '''

                    echo "Waiting for application startup..."

                    sleep 60

                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''

                    echo "Checking application health..."

                    curl --fail \
                    ${APP_URL}/actuator/health

                '''
            }
        }

        stage('OWASP ZAP Spider Scan') {
            steps {
                sh '''

                    echo "Starting ZAP Spider Scan"

                    curl \
                    "${ZAP_URL}/JSON/spider/action/scan/?url=${APP_URL}"

                    echo "Waiting for Spider completion"

                    while true

                    do

                        STATUS=$(curl -s \
                        "${ZAP_URL}/JSON/spider/view/status/" \
                        | jq -r '.status')

                        echo "Spider Progress : ${STATUS}%"

                        if [ "$STATUS" = "100" ]

                        then

                            break

                        fi

                        sleep 10

                    done

                '''
            }
        }

        stage('OWASP ZAP Active Scan') {
            steps {
                sh '''

                    echo "Starting Active Scan"

                    curl \
                    "${ZAP_URL}/JSON/ascan/action/scan/?url=${APP_URL}"

                    echo "Waiting for Active Scan completion"

                    while true

                    do

                        STATUS=$(curl -s \
                        "${ZAP_URL}/JSON/ascan/view/status/" \
                        | jq -r '.status')

                        echo "Active Scan Progress : ${STATUS}%"

                        if [ "$STATUS" = "100" ]

                        then

                            break

                        fi

                        sleep 15

                    done

                '''
            }
        }

        stage('Generate ZAP Report') {
            steps {
                sh '''

                    echo "Generating HTML Report"

                    curl \
                    "${ZAP_URL}/OTHER/core/other/htmlreport/" \
                    -o zap-report.html

                    ls -lh zap-report.html

                '''
            }
        }

        stage('Check ZAP Security Alerts') {
            steps {
                sh '''

                    echo "Checking ZAP Alerts"

                    HIGH_ALERTS=$(curl -s \
                    "${ZAP_URL}/JSON/alert/view/alerts/" \
                    | jq '[.alerts[] | select(.risk=="High")] | length')

                    echo "High Risk Alerts : ${HIGH_ALERTS}"

                    if [ "$HIGH_ALERTS" -gt 0 ]

                    then

                        echo "High risk vulnerabilities found"

                        exit 1

                    fi

                    echo "No High Risk vulnerabilities found"

                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'zap-report.html',
            fingerprint: true

            echo 'Cleaning workspace'

            deleteDir()
        }

        success {
            echo 'Deployment Completed Successfully'
        }

        failure {
            echo 'Deployment Failed'
        }
    }
}
