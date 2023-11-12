node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build Docker image') {
  
       app = docker.build("192.168.1.125:9091/argocd-dev/v1:${env.BUILD_NUMBER}")
    }

    stage('Test Docker image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    // stage('Push image to Harbor') {
    //     sh 'docker login -u admin -p jaime http://192.168.1.125:8081/repository/argocd-dev/'
    //         app.push("${env.BUILD_NUMBER}")
    // }
    stage('Push image to Nexus Docker Repository') {
    withCredentials([usernamePassword(credentialsId: 'nexus-repo', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin http://192.168.1.125:8081/repository/argocd-dev/"
        app.push("${env.BUILD_NUMBER}")
        sh "docker logout http://192.168.1.125:8081/repository/argocd-dev/"
    }
}
    stage('Trigger Update Manifest') {
        echo "triggering Update manifest Job"
            build job: 'argocd-update-manifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
    
}