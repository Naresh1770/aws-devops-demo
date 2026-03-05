pipeline{
    agent any
    triggers{
        pollSCM('* * * * *')
    }
    tools{
        nodejs 'Node18'
    }
    stages{
        stage('Checkout'){
            steps{
                git url: 'https://github.com/Naresh1770/aws-devops-demo.git',
                    branch: 'main'
            }
        }
        stage('Install dependecies'){
            steps{
                sh 'npm install'
            }
        }
        stage('Build Image'){
            steps{
                sh 'docker build -t naresh1770/node-s3:v1 .'
            }
        }
        stage('Docker Login'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: 'docker_hub',
                    usernameVariable: 'USERNAME',
                    passwordVariable: 'PASSWORD'
                )]){
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }
        stage('Image Push'){
            steps{
                sh 'docker push naresh1770/node-s3:v1'
            }
        }
       stage('Deploy'){
            steps{
                sh '''
                docker rm -f nodejs_pro || true
                docker run -d -p 3001:3000 --name nodejs_pro naresh1770/node-s3:v1
                '''
            }
        } 
        stage('Create Artifact'){
            steps{
                sh 'zip -r app.zip .'
            }
        }
        stage('Upload Artifact to S3'){
            steps{
                sh 'aws s3 cp app.zip s3://naresh-devops-artifacts/'
            }
        }
    }
    post{
        success{
            echo 'Pipeline is completed successfully'
        }
        failure{
            echo 'Pipeline failed'
        }
    }
}