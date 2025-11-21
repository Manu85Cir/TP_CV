pipeline {
      agent any
      stages{
        // Création image
        stage('Etape 0 - Stop and delete cv_triaud_cont') {
            steps {
                //sh 'docker stop cv_triaud_cont'
                sh 'docker rm -f cv_triaud_cont'
            }
            post {
                success {
                    echo "====++++Container cv_triaud_cont stopped and delete with success++++===="
                }
                failure {
                    echo "====++++Docker failed to stop/delete failed++++===="
                }
            }
        }
        // Création image
        stage('Création de image docker build jenkins webhook') {
            steps {
                sh 'docker build -t cv_triaud .'
            }
            post {
                success {
                    echo "====++++Docker image created with success++++===="
                }
                failure {
                    echo "====++++Docker image failed++++===="
                }
            }
        }

          // Création image
        stage('Lancer un container de cette image') {
            steps {
                sh 'docker run -d -p 8184:80 --name cv_triaud_cont cv_triaud'
            }
            post {
                success {
                    echo "====++++Container started with success++++===="
                }
                failure {
                    echo "====++++Failed to start Container++++===="
                }
            }
        }
      }
}