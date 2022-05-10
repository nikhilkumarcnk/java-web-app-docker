node{
     def buildNumber = BUILD_NUMBER
    stage('SCM Checkout'){
        git url: 'https://github.com/nikhilkumarcnk/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "maven", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
         sh "docker build -t cnikhilkumar/java-web-app:${buildNumber} ."
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'Docker_Hub_Pwd', variable: 'Docker_Hub_Pwd')]) {
          sh "docker login -u cnikhilkumar -p ${Docker_Hub_Pwd}"
        }
        sh "docker push cnikhilkumar/java-web-app:${buildNumber}"
     }
     
      stage('Run Docker Image In Dev Server'){
        
        def dockerRun = ' docker run  -d -p 8080:8080 --name java-web-app cnikhilkumar/java-web-app:${buildNumber}'
         
         sshagent(['DOCKER_SERVER']) {
          sh 'ssh -o StrictHostKeyChecking=no ubuntu@3.110.185.101 docker stop java-web-app || true'
          sh 'ssh  ubuntu@3.110.185.101 docker rm java-web-app || true'
          sh "ssh  ubuntu@3.110.185.101 docker rmi -f  $(docker images -q) || true"
          sh "ssh  ubuntu@3.110.185.101 ${dockerRun}"
       }
       
    }
     
     
}
