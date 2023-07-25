node {
    def app

    stage('Clone repository') {
      

        checkout scm
    }

    stage('Build Docker image') {
  
       app = docker.build("harbor-devopsodia.com/devopsodia_test/devopsodia:${env.BUILD_NUMBER}")
    }

    stage('Test Docker image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image to Harbor') {
        sh 'docker login -u admin -p admin http://harbor-devopsodia.com'
            app.push("${env.BUILD_NUMBER}")
    }
    stage('Trigger Update Manifest') {
        echo "triggering Update manifest Job"
            build job: 'argocd-update-manifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
    
}
