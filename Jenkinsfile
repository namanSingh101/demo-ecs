@Library('Shared') _
pipeline{
    agent {label 'worker-agent'}

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '214206925153'
        ECR_REPO = 'naman/demo-ecs-project'
        IMAGE_TAG = '1.0'
        IMAGE_NAME = 'doc105b/notes_app'

        ECR_URI = '${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}'
    }

    stages{
        stage('Clonning'){
            steps{
                script{
                    clone('main','')
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
                docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}
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
            echo "✅ Image pushed successfully to ECR: ${ECR_URI}:${IMAGE_TAG}"
        }
        failure {
            echo "❌ Pipeline failed"
        }
    }

}








// pipeline{
//     agent { label 'dev-server' }
    
//     stages{
//         stage("Code Clone"){
//             steps{
//                 echo "Code Clone Stage"
//                 git url: "https://github.com/LondheShubham153/node-todo-cicd.git", branch: "master"
//             }
//         }
//         stage("Code Build & Test"){
//             steps{
//                 echo "Code Build Stage"
//                 sh "docker build -t node-app ."
//             }
//         }
//         stage("Push To DockerHub"){
//             steps{
//                 withCredentials([usernamePassword(
//                     credentialsId:"dockerHubCreds",
//                     usernameVariable:"dockerHubUser", 
//                     passwordVariable:"dockerHubPass")]){
//                 sh 'echo $dockerHubPass | docker login -u $dockerHubUser --password-stdin'
//                 sh "docker image tag node-app:latest ${env.dockerHubUser}/node-app:latest"
//                 sh "docker push ${env.dockerHubUser}/node-app:latest"
//                 }
//             }
//         }
//         stage("Deploy"){
//             steps{
//                 sh "docker compose down && docker compose up -d --build"
//             }
//         }
//     }
// }
