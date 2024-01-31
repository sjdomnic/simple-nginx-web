pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCESS_KEY_ID = credentials('AKIAQ3LVIM6K5PRTGIO5')
        AWS_SECRET_ACCESS_KEY = credentials('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')
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_IMAGE_NAME = 'nginx-web-app'
        DOCKER_IMAGE_TAG = 'latest'
        CODEDEPLOY_APPLICATION_NAME = 'Sample_Nginx'
        CODEDEPLOY_DEPLOYMENT_GROUP = 'Sample_Nginx_DG'
        GITHUB_CREDENTIALS = credentials('github-access-token')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    //git 'https://github.com/sjdomnic/simple-nginx-web.git'
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/sjdomnic/simple-nginx-web.git', credentialsId: GITHUB_CREDENTIALS]]])

                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
                    docker.withRegistry("${DOCKER_REGISTRY}", 'docker-registry-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to CodeDeploy') {
            steps {
                script {
                    sh 'aws deploy create-deployment --application-name ${CODEDEPLOY_APPLICATION_NAME} --deployment-group-name ${CODEDEPLOY_DEPLOYMENT_GROUP} --revision revisionType=AppSpecContent,content=appspec.yaml'
                }
            }
        }
    }
}

