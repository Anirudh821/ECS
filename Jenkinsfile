pipeline{
    agent{label "Anirudh"}
    environment{
        AWS_REGION="ap-south-1"
        ACCOUNT_ID="672061768999"
        ECR="${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/app"
        REPO="app"
        CLUSTER="arn:aws:ecs:ap-south-1:672061768999:cluster/emotional-dolphin-r9bn9u"
        SERVICE="1768999:task-definition/strong-service-v7i2mo:1"
        IMAGE_TAG="{env.GIT_COMMIT.take(7)}"
    }
    stages{
        stage("get url"){
            steps{
                url:"https://github.com/Anirudh821/ECS.git",branch:"main"
            }
        }
        stage("docker build"){
            steps{
                sh "docker build -t ${REPO}:${IMAGE_TAG} ."
            }
        }
        stage("ECR login and code push to ECR"){
            steps{
                sh """
                    aws ecr get-username-password --region ${AWS_REGION} | \
                    docker login -username AWS -password-stdl ${ECR}
                    docker tag ${REPO}:${IMAGE_TAG} ${ECR}/${REPO}:${IMAGE_TAG}
                   """ 
            }
        }
        stage("ECS deployment"){
            steps{
                sh """ 
                        aws ecs update-service \
                        --cluster ${CLUSTER} \
                        --service ${SERVICE} \
                        --force-new-deployment \
                        --region ${REGION}

                        aws ecs wait service-stable\
                        --cluster ${CLUSTER} \
                        --service ${SERVICE} \
                        --region ${REGION}
                """
            }
        }
        post{
            sucess{
                echo "Deployment Sucessful"
            }
            failure{
                echo "Deployment failure"
            }

        }
    }
}