@Library('Shared') _
pipeline{
    agent {label 'worker-agent'}

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '214206925153'
        ECR_REPO = 'naman/demo-ecs-project'
        IMAGE_TAG = '1.0'
        IMAGE_NAME = 'doc105b/notes_app'

        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
    }

    stages{
        stage('Clonning'){
            steps{
                script{
                    clone('main','https://github.com/namanSingh101/demo-ecs.git')
                }
                
            }
        }
        stage('Build'){
            steps{
                script{
                    build(IMAGE_NAME,IMAGE_TAG)
                }
            }
        }
        stage('Login to ECR'){
        steps{
            sh """
                aws ecr get-login-password --region ${AWS_REGION} \
                | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                """
        }
        }
        stage('Tag image for ECR'){
            steps{
                sh """
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}
                """
            }
        }
        stage('Push image to ECR'){
            steps{
                script{
                    pushImage(ECR_URI,IMAGE_TAG)
                }
            }
        }

    }
       post {
        always {
            sh "docker system prune -f"
        }
        success {
            echo "Image pushed successfully to ECR: ${ECR_URI}:${IMAGE_TAG}"
        }
        failure {
            echo " Pipeline failed"
        }
    }

}








