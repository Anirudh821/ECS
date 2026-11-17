pipeline{
    agent{label "Anirudh"}
    environment{
        AWS_REGION="ap-south-1"
        ACCOUNT_ID="672061768999"
        ECR="${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        REPO="app"
        CLUSTER="emotional-dolphin-r9bn9u"
        SERVICE="strong-service-v7i2mo"
        IMAGE_TAG="${env.GIT_COMMIT.take(7)}"
        TASK_FAMILY = "strong-service-v7i2mo"
    }
    stages{
        stage("get url"){
            steps{
                git url:"https://github.com/Anirudh821/ECS.git",branch:"main"
            }
        }
        stage("docker build"){
            steps{
                sh "docker build -t ${REPO}:${IMAGE_TAG} ."
            }
        }
        stage("ECR login and code push to ECR"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'aws-creds',
                         usernameVariable: 'AWS_ACCESS_KEY_ID',
                         passwordVariable: 'AWS_SECRET_ACCESS_KEY')])
                {
                  sh """
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR}
                    docker tag ${REPO}:${IMAGE_TAG} ${ECR}/${REPO}:${IMAGE_TAG}
                    docker push  ${ECR}/${REPO}:${IMAGE_TAG}
                   """
                }
                 
            }
        }
        stage("ECS deployment"){
            steps{
                    withCredentials([usernamePassword(credentialsId: 'aws-creds',
                         usernameVariable: 'AWS_ACCESS_KEY_ID',
                         passwordVariable: 'AWS_SECRET_ACCESS_KEY')])
                {
                  sh """ 
                        aws ecs update-service \
                        --cluster ${CLUSTER} \
                        --service ${SERVICE} \
                        --force-new-deployment \
                        --region ${AWS_REGION}

                
                    """
                }
            }
        }
        
    }
    post{
            success{
                echo "Deployment Sucessful"
            }
            failure{
                echo "Deployment failure"
            }

        }
}