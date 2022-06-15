node{
     
    stage("Git clone"){
       git branch: 'main', credentialsId: 'sai', url: 'https://github.com/saipraasnth/jenkins-testpipelne-1'
    }
    
    stage(" Maven Build jar"){
      
      def mavenHome =  tool name: "Maven 3.8.6", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
    } 
    stage('Build Docker Image'){
        sh "docker build -t dockeridsai9063/naga:1.0.0 ."
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'sai9063', variable: 'sai9063')]) {
          sh "docker login -u dockeridsai9063 -p ${DOCKER_HUB_PASS}"
        }
          sh 'docker push dockeridsai9063/naga:1.0.0'
     }
     
     stage("Deploy To Kuberates Cluster"){
       kubernetesDeploy(
         configs: 'springBootMongo.yml', 
         kubeconfigId: 'KUBERNATES_CONFIG',
         enableConfigSubstitution: true
        )
     }
	 
	  /**
      stage("Deploy To Kuberates Cluster"){
        sh 'kubectl apply -f springBootMongo.yml'
      } **/
     
}

