node{
//node('master'){

  //http://JenkinsServerIPAddress:8080/pipeline-syntax/globals#currentBuild
  //Getting the  env  global varibale values

  echo "GitHub BranhName ${env.BRANCH_NAME}"
  echo "Jenkins Job Number ${env.BUILD_NUMBER}"
  echo "Jenkins Node Name ${env.NODE_NAME}"
  
  echo "Jenkins Home ${env.JENKINS_HOME}"
  echo "Jenkins URL ${env.JENKINS_URL}"
  echo "JOB Name ${env.JOB_NAME}"
  
  properties([
    buildDiscarder(logRotator(numToKeepStr: '3')),
    pipelineTriggers([
        pollSCM('* * * * *')
    ])
  ])
  
  def mavenHome=tool name: "mavenv3.1.1.1", type: "maven"
    
  stage('CheckouttheCode') {
   git branch: 'master', credentialsId: '4d6512c4-c101-4f43-aac5-5860f5d9e20c', url: 'https://github.com/MithunTechnologiesDevOps/maven-web-application.git'  
 }
  /*
   stage('Checkout'){
     checkout scm
  }
  */
 
 stage('Build')
 {
  sh  "${mavenHome}/bin/mvn clean package"
 }

 /* 
 stage('Testing')
   {
    if(isUnix()){
     sh 'mvn test'
      }
      else{
       bat 'mvn test'   
      }
   }
 */

 stage('SonarQubeReport')
 {
  sh  "${mavenHome}/bin/mvn sonar:sonar"
 }

  stage('UploadArtifactsIntoNexus')
 {
  sh  "${mavenHome}/bin/mvn deploy"
 }
 /*
 stage('DeplotoTomcat'){
     
     sh "cp $WORKSPACE/target/*.war /opt/apache-tomcat-9.0.16/webapps/"
 }
 */

stage('DeploytoTomcat'){

   sshagent(['9554f0b8-eda0-486d-9d23-ce7585c32f70']) 
   {
     sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.235.70.188:/opt/apache-tomcat-9.0.22/webapps/maven-web-application.war"
   }
}
 stage('EmailNotification'){
    mail to: 'devopstrainingblr@gmail.com',
         bcc: 'devopstrainingblr@gmail.com', 
         cc: 'devopstrainingblr@gmail.com', 
         from: 'devopstrainingblr@gmail.com', 
         replyTo: 'devopstrainingblr@gmail.com', 
         subject: 'Build Notification came'
         body: '''Build Done, Please check the build log for more details..
         
                  Regards,
                  Mithun Technologies,
                  9980923226'''
 }
 
 stage("SlackNotification"){
     slackSend baseUrl: 'https://devops-team-bangalore.slack.com/services/hooks/jenkins-ci/', channel: 'build-notification', message: 'Build done through', tokenCredentialId: '12797dc5-eb70-4f19-8e05-8c07bc58d79d'
 }
}
