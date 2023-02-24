pipeline {
  agent any
  // any, none, label, node, docker, dockerfile, kubernetes
  tools {
    maven 'my_maven'
  }
  
  environment {
    gitName = 'ojwsakin'
    gitEmail = 'sakin3663@gmail.com'
    gitWebaddress = 'https://github.com/ojwsakin/sb_code.git'
    gitSshaddress = 'git@github.com:ojwsakin/sb_code.git'
    gitCredential = 'git_cre' // github credential 생성시의 ID
    dockerHubRegistry = 'sakin1/sbimage'
    dockerHubRegistryCredential = 'docker_cre' // docker credentaial 생성 시의 ID
  }

  stages {
    stage('Checkout Github') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: gitCredential, url: gitWebaddress]]])
      }
      post {
        failure {
          echo 'Repository clone failure'
        }
        success {
          echo 'Repository clone success'
        }
      }
    }
    stage('Maven Build') {
      steps {
        sh 'mvn clean install'
        // maven 플러그인이 미리 설치 되어있어야 함.
      }
      post {
        failure {
          echo 'maven build failure'
        }
        success {
          echo 'maven build success'
        }
      }
    }
    stage('Docker image Build') {
      steps {
        sh "docker build -t ${dockerHubRegistry}:${currentBuild.number} ."
        // sakin1/sbimage:4 이런식으로 빌드
        // currentBuild.number : 젠킨스에서 제공하는 빌드넘버변수
        sh "docker build -t ${dockerHubRegistry}:latest ."
      }
      post {
        failure {
          echo 'docker image build failure'
        }
        success {
          echo 'docker image build success'
        }
      }
    }
    stage('Docker image push') {
      steps {
        withDockerRegistry(credentialsId: dockerHubRegistryCredential, url: '') {
          // withDockerRegistry : docker pipeline 플러그인 설치시 사용가능.
          // dockerHubRegistryCredential : environment에서 선언한 docker_cre  
            sh "docker push ${dockerHubRegistry}:${currentBuild.number}"
            sh "docker push ${dockerHubRegistry}:latest"
        }
      }
      post {
        failure {
          echo 'docker image push failure'
        }
        success {
          echo 'docker image push success'
        }
      }
    }
  }
}
