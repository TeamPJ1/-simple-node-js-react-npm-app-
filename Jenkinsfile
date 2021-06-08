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
            }
        }
        stage('Test') {
            steps {
                sh "chmod +x -R ${env.WORKSPACE}"
                sh "ls -l ${env.WORKSPACE}"
                sh './jenkins/scripts/test.sh'
                step([$class: 'Mailer', recipients: 'admin@somewhere'])
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
                submitter "alice,bob"
                parameters {
                    boolean(name: 'DEPLOY', defaultValue: 'false', description: 'Approuve ?')
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
