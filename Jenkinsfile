pipeline {
    agent any
    environment {
     
        // Configuration Email
        EMAIL_RECIPIENTS = 'etriaud@cirilgroup.com'
        EMAIL_FROM = '"Jenkins CI/CD" <jenkins-ci@smart-it-partner.com>'
        DOCKERHUB_CREDENTIALS = credentials('emmanueltriaud')
    }
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
        stage('Etape 1 : Création de image docker build jenkins webhook') {
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
        stage('Etape 2 : Lancer un container de cette image') {
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
        stage('Etape 3 : Tag and push image to dockerhub de emmanueltriaud') {
                steps {
                    echo "tag and push image ..."
                    sh "docker tag cv_triaud emmanueltriaud/cv_triaud:v2"
                    sh "docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW"
                    sh "docker push emmanueltriaud/cv_triaud:v2"
                    sh "docker logout"
                }
                post {
                    success {
                        echo "====++++success++++===="
                    }
                    failure {
                        echo "====++++failed++++===="
                    }
                }
          }
     }
     post {
        always {
            script {
                // Variables dynamiques pour l'email
                def buildStatus = currentBuild.result ?: 'SUCCESS'
                def buildDuration = currentBuild.durationString.replace(' and counting', '')
                def buildUrl = env.BUILD_URL
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                
                // Récupérer le résultat du déploiement
                def deploymentResult = "SUCCESS"
             
                
                // Couleurs et icônes dynamiques
                def statusInfo = [
                    'SUCCESS': [
                        color: '#28a745',
                        icon: '✅',
                        text: 'SUCCÈS',
                        message: 'Le build et le déploiement ont été exécutés avec succès'
                    ],
                    'FAILURE': [
                        color: '#dc3545', 
                        icon: '❌',
                        text: 'ÉCHEC',
                        message: 'Le build ou le déploiement a échoué'
                    ],
                    'UNSTABLE': [
                        color: '#ffc107',
                        icon: '⚠️', 
                        text: 'INSTABLE',
                        message: 'Le build est instable'
                    ],
                    'ABORTED': [
                        color: '#6c757d',
                        icon: '⏹️',
                        text: 'ANNULÉ', 
                        message: 'Le build a été annulé'
                    ]
                ]
                
                def currentStatus = statusInfo[buildStatus] ?: statusInfo['SUCCESS']
                
                // Message personnalisé selon le déploiement
                def deploymentMessage = ""
                def deploymentIcon = ""
                if (deploymentResult == "SUCCESS") {
                    deploymentMessage = "L'application est déployée et accessible"
                    deploymentIcon = "✅"
                } else if (deploymentResult == "FAILED") {
                    deploymentMessage = "Le déploiement a échoué"
                    deploymentIcon = "❌"
                } else {
                    deploymentMessage = "Statut de déploiement inconnu"
                    deploymentIcon = "❓"
                }
                
                emailext (
                    to: "${EMAIL_RECIPIENTS}",
                    from: "${EMAIL_FROM}",
                    subject: "${currentStatus.icon} BUILD ${jobName} #${buildNumber} - ${currentStatus.text}",
                    body: """
                    <!DOCTYPE html>
                    <html>
                    <head>
                        <meta charset="UTF-8">
                        <style>
                            body { 
                                font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
                                margin: 0; 
                                padding: 0; 
                                background-color: #f8f9fa; 
                            }
                            .container { 
                                max-width: 600px; 
                                margin: 0 auto; 
                                background: white; 
                                border-radius: 12px; 
                                box-shadow: 0 4px 20px rgba(0,0,0,0.1); 
                                overflow: hidden; 
                            }
                            .header { 
                                background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); 
                                color: white; 
                                padding: 30px; 
                                text-align: center; 
                            }
                            .header h1 { 
                                margin: 0; 
                                font-size: 26px; 
                                font-weight: 600; 
                            }
                            .status-badge { 
                                display: inline-block; 
                                padding: 10px 20px; 
                                border-radius: 25px; 
                                font-weight: bold; 
                                margin: 15px 0;
                                background-color: ${currentStatus.color};
                                color: white;
                                font-size: 14px;
                                text-transform: uppercase;
                                letter-spacing: 1px;
                            }
                            .content { 
                                padding: 30px; 
                            }
                            .info-grid {
                                display: grid;
                                grid-template-columns: 1fr 1fr;
                                gap: 15px;
                                margin: 25px 0;
                            }
                            .info-card {
                                background: #f8f9fa;
                                padding: 15px;
                                border-radius: 8px;
                                border-left: 4px solid #667eea;
                            }
                            .info-label {
                                font-size: 12px;
                                color: #666;
                                text-transform: uppercase;
                                font-weight: 600;
                                margin-bottom: 5px;
                            }
                            .info-value {
                                font-size: 16px;
                                font-weight: 600;
                                color: #333;
                            }
                            .button { 
                                display: inline-block; 
                                padding: 12px 30px; 
                                background: #667eea; 
                                color: white; 
                                text-decoration: none; 
                                border-radius: 6px; 
                                margin: 10px 5px; 
                                font-weight: 600;
                            }
                            .footer { 
                                background: #f8f9fa; 
                                padding: 20px; 
                                text-align: center; 
                                color: #666; 
                                font-size: 12px; 
                                border-top: 1px solid #e9ecef;
                            }
                            .message-box {
                                background: ${currentStatus.color}15;
                                border: 1px solid ${currentStatus.color};
                                border-radius: 8px;
                                padding: 15px;
                                margin: 20px 0;
                                text-align: center;
                            }
                            .message-text {
                                color: ${currentStatus.color};
                                font-weight: 600;
                                margin: 0;
                            }
                            .deployment-info {
                                background: #e7f3ff;
                                border: 1px solid #b3d9ff;
                                border-radius: 8px;
                                padding: 15px;
                                margin: 20px 0;
                            }
                        </style>
                    </head>
                    <body>
                        <div class="container">
                            <div class="header">
                                <h1>🚀 RAPPORT DE DÉPLOIEMENT</h1>
                                <div class="status-badge">
                                    ${currentStatus.icon} ${currentStatus.text}
                                </div>
                            </div>
                            
                            <div class="content">
                                <div class="message-box">
                                    <p class="message-text">${currentStatus.message}</p>
                                </div>
                                
                                <div class="info-grid">
                                    <div class="info-card">
                                        <div class="info-label">Projet</div>
                                        <div class="info-value">${jobName}</div>
                                    </div>
                                    <div class="info-card">
                                        <div class="info-label">Build #</div>
                                        <div class="info-value">${buildNumber}</div>
                                    </div>
                                    <div class="info-card">
                                        <div class="info-label">Statut</div>
                                        <div class="info-value" style="color: ${currentStatus.color}">${currentStatus.text}</div>
                                    </div>
                                    <div class="info-card">
                                        <div class="info-label">Durée</div>
                                        <div class="info-value">${buildDuration}</div>
                                    </div>
                                </div>
                       
                                <div style="background: #fff3cd; border: 1px solid #ffeaa7; border-radius: 6px; padding: 15px; margin: 20px 0;">
                                    <strong>📝 Informations Techniques :</strong><br>
                                    • Email généré automatiquement par Jenkins CI/CD<br>
                                    • Date: ${new Date().format('dd/MM/yyyy à HH:mm:ss')}<br>
                                    • Environnement: Déploiement Automatisé
                                </div>
                            </div>
                            
                            <div class="footer">
                                <p>📧 Cet email a été envoyé automatiquement par Jenkins CI/CD<br>
                                <small>Ne pas répondre à cet email</small></p>
                            </div>
                        </div>
                    </body>
                    </html>
                    """,
                    mimeType: 'text/html',
                    attachLog: true,
                    compressLog: true
                )
                
                echo "📧 Email de rapport envoyé à: ${EMAIL_RECIPIENTS}"
            }
        }
        
        success {
            echo "🎉 Pipeline terminé avec succès - Application déployée sur vps"
        }
        
        failure {
            echo "💥 Pipeline en échec - Vérifiez les logs pour plus de détails"
        }
        
        unstable {
            echo "⚠️  Pipeline instable - Certaines étapes ont rencontré des problèmes"
        }
    }
}