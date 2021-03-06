pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="756422856976"
        AWS_DEFAULT_REGION="us-east-2" 
        IMAGE_REPO_NAME="jenkins-pipeline-build-demo"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
   
    stages {
        stage('Logging into AWS ECR') {
            steps {
                script {
                    sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }

        stage('Cloning Git') {
            steps {
                checkout scm
            }
        }

        // stage('Cloning Git') {
        //     steps {
        //         checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]])     
        //     }
        // }
  
// Building Docker images
        stage('Building image') {
            steps{
                script {
                    dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
   
// Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps{  
                script {
                    sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                    sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
                }
            }
        }
        stage('Clear WS') {
            steps{  
                cleanWs()
            }
        }
    }
}



// pipeline {
//   environment {
//     imagename = "testing upload to ECR"
//     ecrurl = "https://828556645578.dkr.ecr.us-east-2.amazonaws.com"
//     ecrcredentials = "ecr:us-east-2:ecr-ismail"
//     dockerImage = ''
//   } 
//   agent any
//   stages {
//     stage('Cloning Git') {
//       steps {
//                 checkout scm

//       }
//     }
//     stage('Building image') {
//       steps{
//         script {
//           dockerImage = docker.build imagename
//         }
//       }
//     }
   
// stage('Deploy Main Image') {
//    when {
//       anyOf {
//             branch 'main'
//       }
//      }
//       steps{
//         script {
//           docker.withRegistry(ecrurl, ecrcredentials) {     
//             dockerImage.push("$BUILD_NUMBER")
//              dockerImage.push('latest')

//           }
//         }
//       }
//     }

 
//     stage('Remove Unused docker image - Main') {
//       when {
//       anyOf {
//             branch 'main'
//       }
//      }
//       steps{
//         sh "docker rmi $imagename:$BUILD_NUMBER"
//          sh "docker rmi $imagename:latest"

//       }
//     } // End of remove unused docker image for master
//   }  
// } //end of pipeline