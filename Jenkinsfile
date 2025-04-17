pipeline {
    agent { label "habib-node" }
    
    parameters {
        string(name: 'APP_NAME', defaultValue: 'register-app-pipeline')
        string(name: 'RELEASE', defaultValue: '')
        string(name: 'DOCKER_USER', defaultValue: '')
        string(name: 'IMAGE_NAME', defaultValue: '')
        string(name: 'IMAGE_TAG', defaultValue: '')
    }


    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'main', credentialsId: 'github', url: 'https://github.com/SirSaifUrRahman/gitops-register-app.git'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i "s|^\\(\\s*image:\\s*${DOCKER_USER}/${APP_NAME}:\\).*|\\1${IMAGE_TAG}|" deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                        sh '''
                            echo "Checking repository status: "
                            git status

                            echo "Adding changes to git: "
                            git add .

                            echo "Committing changes: "
                            git commit -m "Updated Deployment Manifest" || echo "Nothing to commit"

                            echo "Pushing changes to GitHub: "
                            git push "https://github.com/SirSaifUrRahman/gitops-register-app.git" main
                        '''
                    }
                }
            }
        }

      
    }

    post {
        success {
            script {
                emailext attachLog: true,
                from: 'ssaifurrahman21@gmail.com',
                subject: "Register Application has been updated and deployed - '${currentBuild.result}'",
                body: """
                    <html>
                    <body>
                        <div style="background-color: #FFA07A; padding: 10px; margin-bottom: 10px;">
                            <p style="color: black; font-weight: bold;">Project: ${env.JOB_NAME}</p>
                        </div>
                        <div style="background-color: #90EE90; padding: 10px; margin-bottom: 10px;">
                            <p style="color: black; font-weight: bold;">Build Number: ${env.BUILD_NUMBER}</p>
                        </div>
                        <div style="background-color: #87CEEB; padding: 10px; margin-bottom: 10px;">
                            <p style="color: black; font-weight: bold;">URL: ${env.BUILD_URL}</p>
                        </div>
                    </body>
                    </html>
            """,
            to: 'ssaifurrahman21@gmail.com',
            mimeType: 'text/html'
            }
        }
      failure {
            script {
                emailext attachLog: true,
                from: 'ssaifurrahman21@gmail.com',
                subject: "Register Application build failed - '${currentBuild.result}'",
                body: """
                    <html>
                    <body>
                        <div style="background-color: #FFA07A; padding: 10px; margin-bottom: 10px;">
                            <p style="color: black; font-weight: bold;">Project: ${env.JOB_NAME}</p>
                        </div>
                        <div style="background-color: #90EE90; padding: 10px; margin-bottom: 10px;">
                            <p style="color: black; font-weight: bold;">Build Number: ${env.BUILD_NUMBER}</p>
                        </div>
                    </body>
                    </html>
            """,
            to: 'ssaifurrahman21@gmail.com',
            mimeType: 'text/html'
            }
        }
    }
}
