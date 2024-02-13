def call() {
    pipeline {
    agent any

     options {
      buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        PATH="${PATH}:/opt/maven/bin"
        DOCKER_HUB_CREDENTIALS_ID = "de2f615f-46ce-4439-b815-b671c375bc7f"
        IMAGE_NAME="mavenpipelineimage"
        docker_repo="linuxtraining123"
        APP_NAME = 'maven-app'
        VERSION  = '1.0.0'
    }

    stages {

        stage ('Build DisplayName')  {
            steps {
                script {
                    env
                def buildDisplayName = currentBuild.displayName
                def branchName = env.BRANCH_NAME
                echo "Current branch name: ${branchName}"
                currentBuild.displayName = "${APP_NAME}-${BUILD_NUMBER}"
                }
                }

         } 
        stage('Git Clone my Maven Repository') {
            steps {
                git branch: 'main', credentialsId: '4b8704da-0a6b-4489-b2d7-477c62f7026b', url: 'https://github.com/linuxtraining123/maven-web-app.git'

            }
        }

        stage('Maven Build package') {
            steps {
                sh '''
                mvn clean package
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Define build number 
                    def buildNumber = env.BUILD_NUMBER
                    env.BUILD_NUMBER_ENV = buildNumber
                    echo "BUILD_NUMBER_ENV: ${env.BUILD_NUMBER_ENV}"


                    withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS_ID, usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]){

                    sh '''
                    env
                    docker login -u ${DOCKER_HUB_USERNAME} -p ${DOCKER_HUB_PASSWORD} 
                    docker build -t ${IMAGE_NAME}-${BUILD_NUMBER} .
                    docker image ls 
                    docker tag ${IMAGE_NAME}-${BUILD_NUMBER} ${docker_repo}/${IMAGE_NAME}:${BUILD_NUMBER}
                    docker image ls
                    docker push ${docker_repo}/${IMAGE_NAME}:${BUILD_NUMBER}
                    '''
                   }
                }
            }
        }
    }
}
}
