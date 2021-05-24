node() {
    
   stage('Checkout') {
        cleanWs();
      
        git url: 'git@github.com:nerdulent/sample-spring-microservices.git', 
        credentialsId: 'github-jenkins-keys'
   }
   

   stage('Compile'){
        sh 'mvn clean compile'
   }
   
   stage('Code Analysis - Sonarqube'){
       sh 'mvn clean verify sonar:sonar'
   }
   
   stage('Build Microservices'){
       sh 'mvn clean package'
   }

   stage('Build Docker Images'){
        sh """
            cd $WORKSPACE/account-service; 
            docker build -t vijay666/account-microservice:${env.BUILD_NUMBER} .;
            
            cd $WORKSPACE/customer-service;
            docker build -t vijay666/customer-microservice:${env.BUILD_NUMBER} .;
            
            cd $WORKSPACE/discovery-service;
            docker build -t vijay666/discovery-microservice:${env.BUILD_NUMBER} .;
            
            cd $WORKSPACE/gateway-service;
            docker build -t vijay666/gateway-microservice:${env.BUILD_NUMBER} .;

	    cd $WORKSPACE/interaction-service;
	    docker build -t vijay666/interaction-microservice:${env.BUILD_NUMBER};
        """
   }
   
   stage('Push Images to DockerHub'){
        sh """
            docker push vijay666/account-microservice:${env.BUILD_NUMBER};
            docker push vijay666/customer-microservice:${env.BUILD_NUMBER};
            docker push vijay666/discovery-microservice:${env.BUILD_NUMBER};
            docker push vijay666/gateway-microservice:${env.BUILD_NUMBER};
	    docker push vijay666/interaction-microservice:${env.BUILD_NUMBER};
        """
   }
   
   stage('Deploy Microservices over GKE'){
       sh """
            kubectl set image deployment/account-service account-service=vijay666/account-microservice:$BUILD_NUMBER
            kubectl set image deployment/customer-service customer-service=vijay666/customer-microservice:$BUILD_NUMBER
            kubectl set image deployment/discovery-service discovery-service=vijay666/discovery-microservice:$BUILD_NUMBER
            kubectl set image deployment/gateway-service gateway-service=vijay666/gateway-microservice:$BUILD_NUMBER
	    kubectl set image deployment/interaction-service interaction-service=vijay666/interaction-microservice:$BUILD_NUMBER
       """
   }
}
