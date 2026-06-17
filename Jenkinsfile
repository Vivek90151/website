node {

    stage('Checkout') {
        checkout scm
    }

    stage('Debug') {
        sh 'pwd'
        sh 'ls -la'
    }

    stage('Build Docker Image') {
        sh "docker build -t ${JOB_NAME}:v1.${BUILD_ID} ."
    }


    stage("Tag Image") {

        sh "docker tag ${JOB_NAME}:v1.${BUILD_ID} vivekbhardwaj581/${JOB_NAME}:v1.${BUILD_ID}"

        sh "docker tag ${JOB_NAME}:v1.${BUILD_ID} vivekbhardwaj581/${JOB_NAME}:latest"
    }


    stage("Docker Login") {

        withCredentials([string(credentialsId: 'dockerhubpassword', variable: 'dockerhubpassword')]) {

            sh "docker login -u vivekbhardwaj581 -p ${dockerhubpassword}"

        }
    }


    stage("Push Image on Docker Hub") {

        sh "docker push vivekbhardwaj581/${JOB_NAME}:v1.${BUILD_ID}"

        sh "docker push vivekbhardwaj581/${JOB_NAME}:latest"
    }


    stage("Cleanup") {

        sh "docker rmi ${JOB_NAME}:v1.${BUILD_ID} || true"

        sh "docker rmi vivekbhardwaj581/${JOB_NAME}:v1.${BUILD_ID} || true"
    }


    stage("Deploy on Kubernetes") {

        sh '''
        kubectl apply -f deployment.yml
        kubectl apply -f service.yml

        kubectl rollout restart deployment website-deployment
        '''
    }
}
