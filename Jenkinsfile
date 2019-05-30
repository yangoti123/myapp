
node {
   def sonarUrl = 'sonar.host.url=http://172.31.15.139:9000'
   def mvn = tool (name: 'maven3', type: 'maven') + '/bin/mvn'
   stage('SCM Checkout'){
    // Clone repo
	git branch: 'master', 
	credentialsId: 'github', 
	url: 'https://github.com/yangoti123/myweb'
   
   }
   
   stage('Sonar Publish'){
	   withCredentials([string(credentialsId: 'sonartoken', variable: 'sonartoken')]) {
        def sonartoken = "sonar.login=${sonartoken}"
        sh "${mvn} sonar:sonar -D${sonarUrl}  -D${sonartoken}"
	 }
      
   }
   
	
   stage('Mvn Package'){
	   // Build using maven
	   
	   sh "${mvn} clean package deploy"
   }
   
   stage('deploy-dev'){
       def tomcatDevIp = '172.31.23.143'
	   def tomcatHome = '/opt/tomcat/'
	   def webApps = tomcatHome+'webapps/'
	   def tomcatStart = "${tomcatHome}bin/startup.sh"
	   def tomcatStop = "${tomcatHome}bin/shutdown.sh"
	   
	   sshagent (credentials: ['tomcat-baskar']) {
	      sh "scp -o StrictHostKeyChecking=no target/myweb*.war ec2-user@${tomcatDevIp}:${webApps}myweb.war"
          sh "ssh ec2-user@${tomcatDevIp} ${tomcatStop}"
		  sh "ssh ec2-user@${tomcatDevIp} ${tomcatStart}"
       }
   }
   stage('Email Notification'){
		mail bcc: '', body: """Hi Team, You build successfully deployed
		                       Job URL : ${env.JOB_URL}
							   Job Name: ${env.JOB_NAME}

Thanks,
DevOps Team""", cc: '', from: '', replyTo: '', subject: "${env.JOB_NAME} Success", to: 'ybr4b9@gmail.com'
   
   }
}

