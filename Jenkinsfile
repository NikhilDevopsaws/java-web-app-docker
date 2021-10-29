node{
     
    stage('SCM Checkout'){
        git url: 'https://github.com/vineethalingutla/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven-3.5.6", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
        sh 'docker build -t vineethalingutla/java-web-app-docker .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
          sh "docker login -u vineethalingutla -p ${DockerHubPwd}"
        }
        sh 'docker push vineethalingutla/java-web-app-docker'
     }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name javawebappcontainer vineethalingutla/java-web-app-docker'
         
         sshagent(['DOCKER_SERVER']) {
          sh 'ssh -o StrictHostKeyChecking=no root@172.31.10.147 docker stop javawebappcontainer || true'
          sh 'ssh  root@172.31.10.147 docker rm javawebappcontainer || true'
          sh 'ssh  root@172.31.10.147 docker rmi -f  $(docker images -q) || true'
          sh "ssh  root@172.31.10.147 ${dockerRun}"
       }
       
    }
     
     
}
