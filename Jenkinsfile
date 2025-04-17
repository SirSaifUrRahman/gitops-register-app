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
                   sed -i "s|^\(\s*image:\s*${DOCKER_USER}/${APP_NAME}:\).*|\1${IMAGE_TAG}|" deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "SirSaifUrRahman"
                    git config --global user.email "ssaifurrahman21@gmail.com"
                    git add deployment.yaml
                    git commit -m "Updated Deployment Manifest"
                """
                withCredentials([usernamePassword(
                    credentialsId: 'github',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_TOKEN'
                )]) {
                    sh "git push https://${GIT_USER}:${GIT_TOKEN}@github.com/SirSaifUrRahman/gitops-register-app.git main"
                }

            }
        }
      
    }
}
