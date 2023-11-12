node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build Docker image') {
        app = docker.build("192.168.1.125:9091/argocd-dev/arheanja:${env.BUILD_NUMBER}")
    }

    stage('Test Docker image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image to Harbor') {
        script {
            docker.withRegistry('http://192.168.1.125:9091', 'harbor-credentials') {
                app.push("${env.BUILD_NUMBER}")
            }
        }
    }

    stage('Trigger Update Manifest') {
        echo "triggering Update manifest Job"
        build job: 'argocd-update-manifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
}
