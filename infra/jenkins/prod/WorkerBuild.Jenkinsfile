pipeline {
    agent {
        docker {
            // TODO build & push your Jenkins agent image, place the URL here
            image '352708296901.dkr.ecr.eu-north-1.amazonaws.com/jankins-agent-amip'
            args  '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

       // TODO prod worker build stage

    environment {
        REGISTRY_URL ="352708296901.dkr.ecr.eu-north-1.amazonaws.com"
        IMAGE_TAG = "0.0.$BUILD_NUMBER"
        IMAGE_NAME = "amip-ecr-worker-prod1"
    }

    stages {
        stage('WorkerBuild') {
            steps {
            sh '''
            aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin $REGISTRY_URL
            docker build -f services/worker/Dockerfile -t $IMAGE_NAME .
            docker tag $IMAGE_NAME $REGISTRY_URL/$IMAGE_NAME:$IMAGE_TAG
            docker push $REGISTRY_URL/$IMAGE_NAME:$IMAGE_TAG
            '''
            }
        }
            stage('Trigger Deploy') {
                steps {
                    build job: 'WorkerDeploy', wait: false, parameters: [
                        string(name: 'WORKER_IMAGE_NAME', value: "${REGISTRY_URL}/${IMAGE_NAME}:${IMAGE_TAG}")
                    ]
                }
            }
        }
    }
