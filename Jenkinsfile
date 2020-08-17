pipeline
{
	agent any
	stages 
	{
		stage ('Build Backend')
		{
			steps
			{
				bat 'mvn clean package -DskipTests=true'
			}
		}
		
		stage ('Unit Tests')
		{
			steps
			{
				bat 'mvn test'
			}
		}
		//stage ('Sonar Analysis')
		//{
		//	environment
		//	{
		//		scannerHome = tool 'SONAR_SCANNER'
		//	}
		//	steps
		//	{
		//		withSonarQubeEnv('SONAR_LOCAL')
		//		{
		//			bat "${SCANNERhOME}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.99.100:9000/ -Dsonar.login=cf6826d57f1e453e08ecbd6cf862496472061f66 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/model/**,**Application.java"
		//		}
		//	}
		//}
		//stage('Quality Gat')
		//{
		//		steps
		//		{
		//			sleep(20)
		//			timeout(time: 1, unit: 'MINUTES')
		//			{
		//				waitForQualityGate abortPipelina: true
		//			}
		//		}
		//}
		stage ('Deploy Backend')
		{
			steps
			{
				deploy adapters: [tomcat8(credentialsId: 'TomcatLogin3', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
			}
		}
		stage ('API Test')
		{
			steps
			{
				dir ('api-test')
				{
					git credentialsId: 'github_login', url: 'https://github.com/WilliamMassao/tasks-api-test'
					bat 'mvn test'
				}	
			}
		}
		stage ('Deploy Frontend')
		{
			steps
			{
				dir ('frontend')
				{
					
					git credentialsId: 'github_login', url: 'https://github.com/WilliamMassao/tasks-frontend'
					bat 'mvn clean package'
					deploy adapters: [tomcat8(credentialsId: 'TomcatLogin3', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
				}
			}
		}
		stage ('Functionat Test')
		{
			steps
			{
				dir ('funcionatl-test')
				{
					git credentialsId: 'github_login', url: 'https://github.com/WilliamMassao/tasks-functional-tests'
					bat 'mvn test'
				}	
			}
		}
		stage ('Deploy Producao')
		{
			steps
			{
				bat 'docker-compose build'
				bat 'docker-compose up -d'
			}
		}
		//stage ('Health Check')
		//{
		//	steps
		//	{
		//		sleep(5)
		//		dir ('funcionatl-test')
		//		{
		//			bat 'mvn verify -Dskip.surefire.tests'
		//		}
		//	}
		//}	
	}
	post
	{
		always
		{
			junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, funcionatl-test/target/surefire-reports/*.xml'
			archiveArtifacts artifacts: 'target/tasks-backend.war, frontend/target/tasks.war', onlyIfSuccssful: true
			
			
		}
		unsuccessful
		{
			emailext attachLog: true, body: 'See the attacjed log below', subject: 'Build $BUILD_NUMBER has failed', to: 'william.massao.ftt@gmail.com'
		}
		fixed
		{
			emailext attachLog: true, body: 'See the attacjed log below', subject: 'Build $BUILD_NUMBER is fine!', to: 'william.massao.ftt@gmail.com'
		}
	}
}

