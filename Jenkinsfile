pipeline {
    agent { docker { image 'python:3.7.2' } }
    stages {
        stage('build') {
            steps {
                sh 'pip install flask'
                sh 'pip install xmlrunner'
            }
        }
        stage('Test') {
            steps {
                sh 'python3 test.py'
                input(id: "Deploy Gate", message: "Deploy ${params.project_name}?", ok: 'Deploy')
            }
        }
        
        stage('Deploy') {
            steps {
                echo "deploying the application"
                sh "sudo nohup python3 app.py > log.txt 2>&1 &"
            }
            post {
                always {
                    echo 'The pipeline completed'
                    junit allowEmptyResults: true, testResults:'**/test_reports/*.xml'
                }
                success {                   
                    echo "Flask Application Up and running!!"
                }
                failure {
                    echo 'Build stage failed'
                    error('Stopping early…')
                }
            }
        }
    } 
}
node {
    stages {
        stage('Build Docker') {
            steps {
                sh "sudo docker build -t flask-app ."
            }
        }
    }   
}