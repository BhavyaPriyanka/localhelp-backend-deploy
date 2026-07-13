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
}


