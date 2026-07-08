pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build') {
      steps {
        dir('api') {
          sh 'npm install'
        }
      }
    }

    stage('Test') {
      steps {
        dir('api') {
          sh 'npm test'
        }
      }
    }
    stage('Image') {
      when {
        expression {
          env.GIT_BRANCH == 'origin/main' || env.BRANCH_NAME == 'main'
        }
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'gitlab-registry', usernameVariable: 'U', passwordVariable: 'P')]) {
        sh '''
          echo "$P" | docker login "$REGISTRY" -u "$U" --password-stdin
          docker build -t "$IMAGE:$GIT_COMMIT" ./api
          docker push "$IMAGE:$GIT_COMMIT"
        '''
        }
      }
    }
  }

  post {
    success {
      echo 'Build OK'
    }
    failure {
      echo 'Build en échec — voir les logs'
    }
  }
}
