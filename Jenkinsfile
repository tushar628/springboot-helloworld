node{
    def mvnHome
	try{
    stage('clone_from_git'){
        git 'https://github.com/tushar628/springboot-helloworld.git'
        mvnHome=tool 'MyMaven'
        
    }
    
    stage('maven_build'){
         withEnv(["MVN_HOME=$mvnHome"]){
            echo '$MVN_HOME/bin/mvn clean install'
         }
    }
    stage('sonar_check'){
        withEnv(["MVN_HOME=$mvnHome"]){
            withSonarQubeEnv('mySonar') {
                 echo '$MVN_HOME/bin/mvn sonar:sonar'
         }
    }
    }
    stage('quality_gate_chk'){
             timeout(time: 1, unit: 'HOURS') {
                def qg = waitForQualityGate() 
                if (qg.status != 'OK') { 
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
             }
       
    }
    stage('artefactory_upload'){
        def server =Artifactory.server 'MyArtifactory' 
        def uploadSpec="""{
            "files":[
                        {
                            "pattern":"target/*.war",
                            "target":"repo/"
                        }
                    ]
        }"""
         server.upload(uploadSpec)
    }
    stage('download_artifactory'){
        def server =Artifactory.server 'MyArtifactory' 
        def downloadSpec="""{
            "files":[
                        {
                            "pattern":"repo/*.war",
                            "target":"D:\\tmp\\"
                        }
                    ]
        }"""
        server.download(downloadSpec)
    }
	}
	catch(caughtError){
            currentBuild.result = "FAILURE"
            mail (to: 'Tusharkanta.Sahoo@mindtree.com',
            subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) is failing",
            body: "Hi Tushar, Please visit ${env.BUILD_URL}. This build failied due to some issue.");
    }
}


