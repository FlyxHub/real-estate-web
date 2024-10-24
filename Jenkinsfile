node('AppServer') {
    stage('Pull from GitHub Repo') {
        checkout SCM
    }

    stage('Build and Tag Image') {
        app = docker.build('flyxdocker/real-estate-web')
    }

    stage('Push to DockerHub') {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-creds')
        {
            app.push('latest')
        }
    }

    stage('Deploy Container') {
        sh 'docker-compose down'
        sh 'docker-compose up -d'
    }
}
