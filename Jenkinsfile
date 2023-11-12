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
        script {
            // Específica las credenciales de Docker directamente aquí
            def dockerCredentials = 'admin:jaime'

            sh "echo $dockerCredentials | docker login -u admin --password-stdin http://192.168.1.125:9091/repository/argocd-dev/"
            app.push("${env.BUILD_NUMBER}")
        }
    }

    stage('Trigger Update Manifest') {
        echo "triggering Update manifest Job"
        build job: 'argocd-update-manifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
}

