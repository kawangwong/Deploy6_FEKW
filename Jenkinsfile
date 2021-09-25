pipeline{
    agent {
        label 'worker1'
    }
    stages {
        stage ('Build') {
            steps {
            sh 'rm -rf ./kura_test_repo/cypress2'
            cd './kura_test_repo'
            sh '''
                npm install
                npm run build
                sudo npm install -g serve
                serve -s build
                '''
                }
            }
        stage ('test') {
            agent {
                label 'worker2'
            }
            steps {
            sh '''
                npm install cypress
                npm install mocha
                npx cypress run --spec \
                ./cypress/integration/test.spec.js
                '''
            }
        post {
            always {
                junit 'results/cypress-report.xml'
            }
        }
        }
    }
}
