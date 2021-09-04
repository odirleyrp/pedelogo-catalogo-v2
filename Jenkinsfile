pipeline {
    agent any

    stages {

        stage('Get Source') {
            steps {
            git url: 'https://github.com/odirleyrp/pedelogo-catalogo.git', branch: 'main'
            }
          }

        stage('Docker Build') {
            steps {
                script {
                    dockerapp = docker.build("odirleyrp/pedelogo-catalogo:${env.BUILD_ID}",
                        '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage ('Docker Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy Kubernetes') {
            agent {
              kubernetes {
                cloud 'kubernetes'
          }
        }
          steps {
              kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig' )
          }
        }
    }
}
