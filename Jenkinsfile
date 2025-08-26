pipeline {
  agent any

  environment {
    IMAGE = "netflix-clone:${BUILD_NUMBER}"
    TMDB_KEY = credentials('TMDB_API_KEY')
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/shamashaik19/Netflix-Clone-Project.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh """
          cp .env.example .env || true
          # inject TMDB key into env file
          sed -i 's|TMDB_V3_API_KEY=.*|TMDB_V3_API_KEY=${TMDB_KEY}|' .env

          docker build --build-arg TMDB_V3_API_KEY=${TMDB_KEY} -t ${IMAGE} .
        """
      }
    }

    stage('Run Container') {
      steps {
        sh """
          docker rm -f netflix-clone || true
          docker run -d --name netflix-clone -p 80:80 -e TMDB_V3_API_KEY=${TMDB_KEY} ${IMAGE}
        """
      }
    }
  }

  post {
    success {
      echo "✅Netflix clone deployed successfully! Access it at http://<your-server-ip>"
    }
    failure {
      echo "❌ployment failed. Check logs."
    }
  }
}

