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
  def app = docker.build("${DOCKER_HUB_ACCOUNT}/${DOCKER_IMAGE_NAME}", '.')

 echo 'Testing Docker image'
    stage("test image") {
        docker.image("${DOCKER_HUB_ACCOUNT}/${DOCKER_IMAGE_NAME}").inside {
            sh './test.sh'
        }
    }
   echo 'Pushing Docker Image'
    stage("Push")
    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub') {
        app.push()
    }

}

