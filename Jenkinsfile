pipeline {
    agent none
    stages {
  stage('checkout') {
	when {
	 branch 'main'
	}
	agent { label 'DEV' }
    steps {
     checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pravinspatil-191/Multibranch.git']])
    }
  }

  stage('build') {
	when {
	 branch 'main'
	}
	agent { label 'DEV' }
    steps {
      sh '''
	  pwd
	  ls
    mvn clean package
      '''
    }
  }

  stage('sonar/trivy') {
	when {
	branch 'dev'
	}
	agent { label 'SonarQube' }
    steps {
	withSonarQubeEnv ('sonar'){
			    sh '''
			    pwd
				ls
				mvn clean verify sonar:sonar \
				-Dsonar.projectkey='project1' \
				-Dsonar.projectName=project1
				'''
			}

      sh '''
      ls
      trivy fs java-sample-21-1.0.0.jar
      '''
    }
  }

  stage('deploy') {
	when {
		branch 'main'
	}
	agent { label 'DEV' }
    steps {
      sh '''
      ls
      mkdir -p /home/ubuntu/logs/jenkins
      sudo JENKINS_NODE_COOKIE=dontkillMe nohup java -jar java-sample-21-1.0.0.jar > /home/ubuntu/logs/jenkins/jenkins.log 2>&1 &
      '''
    }
  }

}

}
