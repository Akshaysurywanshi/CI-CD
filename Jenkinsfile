pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/Akshaysurywanshi/CI-CD.git'
        script {
          docker.withRegistry('docker.io/akshaysurywanshi', 'dockerhub') {
            def imageName = "docker.io/akshaysurywanshi/webapp"
            docker.build(imageName, './')
            docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
              docker.image(imageName).push()
            }
          }
        }
      }
    }

    stage('Deploy') {
      steps {
        script {
          def kubeconfig = credentials('kubeconfig')
          def deploymentFile = readFile('deployment.yaml')
          def serviceFile = readFile('service.yaml')

          withKubeConfig(credentialsId: kubeconfig, kubeconfigContent: kubeconfig) {
            sh 'kubectl apply -f - <<EOF\n' + deploymentFile + '\nEOF'
            sh 'kubectl apply -f - <<EOF\n' + serviceFile + '\nEOF'
          }
        }
      }
    }
  }
}
