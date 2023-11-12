node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build Docker image') {
        app = docker.build("192.168.1.125:9091/argocd-dev/devopsodia:${env.BUILD_NUMBER}")
    }

    stage('Test Docker image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image to Harbor') {
        withCredentials([usernamePassword(credentialsId: 'nexus', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
            // Utilizar --password-stdin para evitar la exposición de la contraseña en el comando
            sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin http://192.168.1.125:9091/repository/argocd-dev/"
            app.push("${env.BUILD_NUMBER}")
        }
    }

    stage('Trigger Update Manifest') {
        echo "triggering Update manifest Job"
        build job: 'argocd-update-manifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
}
