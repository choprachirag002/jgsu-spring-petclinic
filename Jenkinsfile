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
         stage('maven-validate'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sh 'mvn validate'
            }
        }
         stage('maven-compile'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sh 'mvn compile'
            }
        }
        stage('maven-test'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sh 'mvn test'
            }
        }
        stage('maven-package'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sh 'mvn package'
            }
        }
        stage('maven-install'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sh 'mvn install'
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
        stage("quality-gate") {
            steps {
                script{
                    last_started=env.STAGE_NAME
                }
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
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
                                "target": "art-doc-dev-local2"
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
                              "pattern": "art-doc-dev-local2/",
                                 "target": "artifacts/"
                             }
                        ]
                    }''',
                )
            }
        }

        stage('aws deployment'){
            steps{
                script{
                    last_started=env.STAGE_NAME
                }
                sshagent(['1c35a59b-1ef3-458c-8c5f-345bf00e1baf']){
                    sh 'scp -r /var/jenkins_home/workspace/pipeline1/artifacts/*.jar ubuntu@3.22.222.54:/home/ubuntu/artifacts'
        }
            }
        } 

    }
        post{
            success{
                mail bcc: '', body: "<b>Pipeline Success</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Last Stage Completed: $last_started <br> URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Pipeline Information System: ${env.JOB_NAME}", to: "samalatib96@gmail.com";
            }
            failure{
                mail bcc: '', body: "<b>Pipeline Failure</b><br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br>Stage Name: $last_started <br> URL: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "Pipeline Information System: ${env.JOB_NAME}", to: "samalatib96@gmail.com";
            }
        }



    }
