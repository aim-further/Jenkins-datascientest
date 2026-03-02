pipeline {

    agent any



    stages {

        stage('Docker Build') {

            steps {

                echo 'Construction de l\'image Docker...'

            }

        }



        stage('Docker run') {

            steps {

                echo 'Lancement du conteneur test...'

                // Ici tu mettrais : sh 'docker run -d mon-image'

            }

        }



        stage('Test Acceptance') {

            steps {

                echo 'Vérification de la conformité de l\'application...'

            }

        }



        stage('Docker Push') {

            steps {

                echo 'Envoi de l\'image sur le registre...'

            }

        }



        stage('Deploiement en dev') {

            steps {

                echo 'Déploiement sur l\'environnement de Développement...'

            }

        }



        stage('Deploiement en staging') {

            steps {

                echo 'Déploiement sur l\'environnement de Staging...'

            }

        }



        stage('Deploiement en prod') {

            steps {

                echo 'Mise en production finale !'

            }

        }

    }



post {
        success {
            mail to: 'seynabou.dieng93@gmail.com',
                 subject: "Succès du Pipeline : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Félicitations ! Le déploiement en production est terminé avec succès."
        }
        failure {
            mail to: 'seynabou.dieng93@gmail.com',
                 subject: "ÉCHEC du Pipeline : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Attention, le pipeline a échoué. Veuillez vérifier les logs sur Jenkins."
        }
    }
}
