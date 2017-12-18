node {
    checkout scm
  def DOCKER_HUB_ACCOUNT = 'chandanakeswarkar'
  def DOCKER_IMAGE_NAME = 'k8s-example'

    echo 'Building Go App'
    stage("build") {
        docker.image("icrosby/jenkins-agent:kube").inside('-u root') {
            sh 'go build' 
        }
    }
    echo 'Testing Go App'
    stage("test") {
        docker.image('icrosby/jenkins-agent:kube').inside('-u root') {
            sh 'go test' 
        }
    }

 echo 'Building Docker image'
  stage('BuildImage') 
  def app = docker.build("${DOCKER_HUB_ACCOUNT}/${DOCKER_IMAGE_NAME}:${BUILD_TAG}", '.')

 echo 'Testing Docker image'
    stage("test image") {
        docker.image("${DOCKER_HUB_ACCOUNT}/${DOCKER_IMAGE_NAME}:${BUILD_TAG}").inside {
            sh './test.sh'
        }
    }
   echo 'Pushing Docker Image'
    stage("Push")
    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
        app.push()
    }

    echo "Deploying image"
    stage("Deploy") 
    docker.image('smesch/kubectl').inside{
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh "kubectl --kubeconfig=$KUBECONFIG apply -f k8s-example.yaml --validate=false"
        }
    }

}

