pipeline
{
	agent any
	stages
	{
		stage('compile stage')
		{
			steps
			{
				bat 'mvn clean compile'
			}
		}

		stage('Sonar analysis')
		{
			steps
			{
				bat 'mvn sonar:sonar -Dsonar.projectKey=sonarpipelinetest -Dsonar.projectName=sonarpipelinetest -Dsonar.host.url=http://localhost:9000 -Dsonar.login=79940f09ee84c726b644ddeb9fcefad5db6020ad'
			}
		}


		stage('testing stage')
		{
			steps
			{
				bat 'mvn test'
			}
		}
	
		stage('package stage')
		{
			steps
			{
				bat 'mvn package'
			}
		}
		
		stage ('Artifactory configuration') {
            steps {
                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ArtifactoryImage",
                    releaseRepo: "jenkins-local-maven-repo",
                    snapshotRepo: "jenkins-local-maven-repo"
                )
            }
        }

        stage ('Artifactory Exec Maven') {
            steps {
                rtMavenRun (
                    tool: "maven_3_6_0", // Tool name from Jenkins configuration
                    pom: 'pom.xml',
                    goals: 'clean package',
                    deployerId: "MAVEN_DEPLOYER",
                )
            }
        }
        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "ArtifactoryImage"
                )
            }
        }
		stage('Docker'){
			steps{				
				bat 'set YYYYMMDD.HHMMSS=%DATE:~6,4%%DATE:~3,2%%DATE:~0,2%.%TIME:~0,2%%TIME:~3,2%%TIME:~6,2%',
				
				bat 'docker build -t asramitsinghrawat/demoapp:%YYYYMMDD.HHMMSS% .',
				bat 'docker push asramitsinghrawat/demoapp:%YYYYMMDD.HHMMSS',
				bat 'docker pull asramitsinghrawat/demoapp:%YYYYMMDD.HHMMSS',
				bat 'docker run -d --rm -p 8087:8080 asramitsinghrawat/demoapp:%YYYYMMDD.HHMMSS'
			}
		}	
	}
}