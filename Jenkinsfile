node{
    stage('Scm Checkout'){
        git(credentialsId: 'andreygalizin', url: 'git@github.com:andreygalizin/jenkins-docker-app.git', branch: 'master')
    }
    stage ('Mvn Package'){
        def mvnHome = tool name: 'maven-3', type: 'maven'
        def mvnCMD = "${mvnHome}/bin/mvn"
        sh "${mvnCMD} clean package"
    }
    stage ('Build Docker Image'){
        sh 'docker build -t andreygalizin/docker-app:2.0.0 .'
    }
    stage ('Push Docker Image'){
        withCredentials([string(credentialsId: 'dockerHubPWD', variable: 'AGdockerHubPwd')]) {
            sh "echo ${AGdockerHubPWD} | docker login -u andreygalizin --password-stdin"
        }
        sh 'docker push andreygalizin/docker-app:2.0.0'
    }
    stage ('Run Container on Dev Server'){
        sshagent(['dev-server']) {
            def dockerRun = 'docker run -p 9090:8080 -d --name my-app andreygalizin/docker-app:2.0.0'
            sh "ssh -o StrictHostKeyChecking=no ubuntu@192.168.211.232 ${dockerRun}"
        }
    }
}