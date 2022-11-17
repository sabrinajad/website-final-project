pipeline {
    agent { label 'slave' }
    environment {
    GCLOUD=credentials('gcloud')
    CONFIG=credentials('kubernetes')
    }
    stages {
        stage('Docker') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "dockerhub" , usernameVariable: 'USER' , passwordVariable: 'PASS')]){
                        sh """
                            docker login -u ${USER} -p ${PASS}
                            docker build -t sabrin9696/final:${BUILD_NUMBER} .
                            docker push sabrin9696/final:${BUILD_NUMBER}
                            echo ${BUILD_NUMBER} > ../build.txt
                           """
                    }
                }
            }
        }
        stage('Deployment') {
            steps {
                        sh """
                           export BUILD_NUMBER=\$(cat ../build.txt)
                           cat Deployment/Website_deployment.yml | envsubst > Deployment/Website_deployment.tmp
                           mv Deployment/Website_deployment.tmp Deployment/Website_deployment.yml
                           gcloud auth activate-service-account thecontroller@sabrinnasr2050.iam.gserviceaccount.com  --key-file="$GCLOUD"
                           gcloud container clusters get-credentials final-project --zone us-central1-a --project sabrinnasr2050
                           kubectl apply -f Deployment --kubeconfig=${CONFIG}
                           """
            }
        }
    }
}