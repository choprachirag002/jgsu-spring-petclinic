pipeline{
    agent any
    
    triggers{
        pollSCM('* * * * *')
    }

    tools{
        maven 'maven'
        jdk 'java11'
    }

    stages{
        stage('maven-clean'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sh 'mvn clean'
            }
        }
        
        stage('sonar-analysis'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                withSonarQubeEnv('sonarqube1'){
                    sh 'mvn sonar:sonar'
                }
            }
        }
        
       
        stage ('deploy to artifactory'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
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
                script{
                    last_started=env.STAGE_NAME
                }
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

        
        post{
            success{
                mail bcc: '', body: "<b>Pipeline Success</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Last Stage Completed: $last_started <br> URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Pipeline Information System: ${env.JOB_NAME}", to: "chopra.chirag002@gmail.com";
            }
            failure{
                mail bcc: '', body: "<b>Pipeline Failure</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Stage Name: $last_started <br> URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Pipeline Information System: ${env.JOB_NAME}", to: "chopra.chirag002@gmail.com";
            }
        }



    }
