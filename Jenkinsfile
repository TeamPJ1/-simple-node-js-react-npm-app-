pipeline {

  
   agent {
        docker {
            image 'node:6-alpine'
            args '-p 3000:3000 -p 5000:5000'
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                mail bcc: '', body: 'message', cc: '', from: '', replyTo: '', subject: 'subject', to: 'adm.sokray@gmail.com'
            }
        }
        stage('Test') {
            steps {
                sh "chmod +x -R ${env.WORKSPACE}"
                sh "ls -l ${env.WORKSPACE}"
                sh './jenkins/scripts/test.sh'
                step([$class: 'Mailer', recipients: 'adm.sokray@gmail.com'])
            }
          
        }

        stage('CodeCheck'){
            steps {
            // sonar_scanner for code static checking
               withSonarQubeEnv('sonar') {
                    script {
                      // requires SonarQube Scanner 4.0+
                      scannerHome = tool 'SonarQube Scanner 4.0'
                     }
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=simple-node-js-react-npm-app \
                    -Dsonar.sources=src/ \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.login=94a6019acc61837c98d7f182e5169503d01d238b
                    """
              }
            }
        }
     
        stage('Deliver') {
            when {
                branch 'main'
                environment name: 'DEPLOY_TO', value: 'production'
            }
            input {
                message "Should we continue to deploy?"
                ok "Yes"
                submitter "admin"
                parameters {
                    booleanParam(name: 'DEPLOY', defaultValue: 'false', description: 'Approuve ?')
                    string(name: 'COMMENT', defaultValue: 'comment here', description: 'Comment')
                }
            }
            steps {
                sh "chmod +x -R ${env.WORKSPACE}"
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
