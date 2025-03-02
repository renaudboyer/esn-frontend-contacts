pipeline {
  agent none

  stages {
    stage('Install packages & Lint & run tests') {
      agent {
        dockerfile {
          filename 'Dockerfile'
          dir 'test'
        }
      }
      steps {
        sh 'npm install'
        sh 'npm run lint'
        sh 'npm run test'
      }
    }

    stage('Release latest image on Docker hub') {
      when { branch 'main' }
      agent {
        docker {
          image 'docker:19.03.12-dind'
          args '-e DOCKER_HOST=$DOCKER_HOST'
        }
      }
      steps {
        script {
          def dockerImage = docker.build 'linagora/esn-frontend-contacts'
          docker.withRegistry('', 'dockerHub') {
            dockerImage.push('main')
          }
        }
      }
      post {
        success {
            echo 'Build OpenPaas front Docker image'
            build wait: false, job: 'openpaas-front/main'
        }
      }
    }

    stage('Release tag on Docker hub') {
      when { tag '*' }
      agent {
        docker {
          image 'docker:19.03.12-dind'
          args '-e DOCKER_HOST=$DOCKER_HOST'
        }
      }
      steps {
        script {
          def dockerImage = docker.build 'linagora/esn-frontend-contacts'
          docker.withRegistry('', 'dockerHub') {
            dockerImage.push(env.TAG_NAME)
          }
        }
      }
    }
  }
}
