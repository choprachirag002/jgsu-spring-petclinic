pipeline{
    agent any
   

    tools{
        maven 'maven'
        jdk 'java11'
    }

    stages{
        stage('maven-clean'){
            steps{
                sh 'mvn clean package'
            }
        }
        
        stage('sonar-analysis'){
            steps{
                
                withSonarQubeEnv('sonarqube1'){
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
       
        stage ('deploy to artifactory'){
            steps{
                
                rtUpload(
                    serverId: 'artifactoryserver',
                    spec: '''{
                        "files":[
                            {
                              "pattern": "target/*.jar",
                                "target": "art-doc-dev-local1"
                             }
                        ]
                    }''',
                )
            }
        } 
        stage ('download to artifacts folder'){
            steps{
                
                rtDownload(
                    serverId: 'artifactoryserver',
                    spec: '''{
                        "files":[
                            {
                              "pattern": "art-doc-dev-local1/",
                                 "target": "artifacts/"
                             }
                        ]
                    }''',
                )
            }
        }
        
        stage('aws deployment'){
            steps{
               
                sshagent(['58061018-9ed5-4a1e-960e-87fc0383adba']){
                    sh 'scp -r /var/jenkins_home/workspace/pipeline1/artifacts/*.jar ubuntu@18.188.155.174:/home/ubuntu/artifacts'
        }
            }
        } 

    }

}


