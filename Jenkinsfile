node() {
    
   stage('Checkout') {
        cleanWs();
      
        git url: 'git@github.com:vijay-mukhil/sample-spring-microservices.git', 
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
            docker build -t jay899/account-microservice:${env.BUILD_NUMBER} .;

	    cd $WORKSPACE/interaction-service;
	    docker build -t jay899/interaction-microservice:${env.BUILD_NUMBER} .;
        """
   }
   
   stage('Push Images to DockerHub'){
        sh """
            docker push jay899/account-microservice:${env.BUILD_NUMBER};
	    docker push jay899/interaction-microservice:${env.BUILD_NUMBER};
        """
   }
   
   stage('Deploy Microservices over AKS'){
       sh """
            kubectl set image deployment/account-service account-service=jay899/account-microservice:$BUILD_NUMBER
            kubectl set image deployment/interaction-service interaction-service=jay899/interaction-microservice:$BUILD_NUMBER
       """
   }
}
