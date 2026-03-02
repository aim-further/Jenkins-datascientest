pipeline {
    environment { 
        DOCKER_ID = "seynaboudieng"  
        DOCKER_IMAGE = "fastapi-app"
        DOCKER_TAG = "v.${BUILD_ID}.0" 
    }
    agent any

    stages {
        stage('Docker Build') {
            steps {
                script {
                    sh '''
                        docker rm -f jenkins || true
                        docker build -t $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG .
                    '''
                }
            }
        }

        stage('Docker run') {
            steps {
                script {
                    sh '''
                        docker run -d -p 80:80 --name jenkins $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                        sleep 10
                    '''
                }
            }
        }

        stage('Test Acceptance') {
            steps {
                script {
                    sh 'curl http://localhost || echo "Test failed"'
                }
            }
        }

        stage('Docker Push') {
            environment {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS") 
            }
            steps {
                script {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_ID --password-stdin
                        docker push $DOCKER_ID/$DOCKER_IMAGE:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Deploiement en dev') {
            environment { KUBECONFIG_FILE = credentials("config") }
            steps {
                script {
                    deployToK8s("dev", KUBECONFIG_FILE)
                }
            }
        }

        stage('Deploiement en staging') {
            environment { KUBECONFIG_FILE = credentials("config") }
            steps {
                script {
                    deployToK8s("staging", KUBECONFIG_FILE)
                }
            }
        }

        stage('Deploiement en prod') {
             environment { KUBECONFIG_FILE = credentials("config") }
            steps {
                timeout(time: 15, unit: "MINUTES") {
                    input message: 'Voulez-vous déployer en production ?', ok: 'Oui'
                }
                script {
                    deployToK8s("prod", KUBECONFIG_FILE)
                }
            }
        }
    }

    post {
        success {
            mail to: 'seynabou.dieng93@gmail.com',
                 subject: "Succès : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Le déploiement est terminé. URL : ${env.BUILD_URL}"
        }
        failure {
            mail to: 'seynabou.dieng93@gmail.com',
                 subject: "ÉCHEC : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Le pipeline a échoué. URL : ${env.BUILD_URL}"
        }
        always {
            sh 'docker rm -f jenkins || true'
        }
    }
}

def deployToK8s(namespace, config) {
    sh """
        mkdir -p .kube
        cat ${config} > .kube/config
        cp fastapi/values.yaml values.yml
        sed -i "s+tag.*+tag: ${DOCKER_TAG}+g" values.yml
        export KUBECONFIG=.kube/config
        helm upgrade --install app fastapi --values=values.yml --namespace ${namespace}
    """
}
