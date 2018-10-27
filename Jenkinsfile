def server
def rtMaven
def buildInfo
pipeline {
	agent any
	stages {
		stage("Artifactory") {
			steps {
				script {
					server  = Artifactory.server 'artifactory'
					rtMaven = Artifactory.newMavenBuild()
					rtMaven.tool = 'M3'
					rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local', server: server
					rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot', server: server
					rtMaven.deployer.artifactDeploymentPatterns.addExclude("pom.xml")
					buildInfo = Artifactory.newBuildInfo() 
					buildInfo.retention maxBuilds: 10, maxDays: 7, deleteBuildArtifacts: true
					buildInfo.env.capture = true
				}			
			}
		}
		stage("Build") {
			steps {
				script {
					sh "echo ${params.dependency1CurrentVersion}"
					sh "echo ${params.dependency1NextVersion}"
					//sh "git config --global user.email \"you@example.com\""
					//sh "git config --global user.name \"Your Name\""
			                rtMaven.run pom: 'pom.xml', goals: "scm:checkin -Dmessage=\"commiting the pom with the release version\" -DpushChanges=false"
					//def pom = readMavenPom file: 'pom.xml'
					//pom.properties['independent'] = params.dependency1CurrentVersion					
					//rtMaven.run pom: 'pom.xml', goals: "scm:tag -Dmessage=\"tag with release version\" -Dtag=\"1.10.2\""
					rtMaven.run pom: 'pom.xml', goals: 'versions:set -DnewVersion="' + params.dependency1NextVersion + '"', buildInfo: buildInfo					
					rtMaven.run pom: 'pom.xml', goals: 'versions:update-property -Dindependent="' + params.dependency1CurrentVersion + '"', buildInfo: buildInfo					
					rtMaven.run pom: 'pom.xml', goals: "scm:checkin -Dmessage=\"updating pom\" -DpushChanges"
							//git ls-remote -h git@bitbucket.org:person/projectmarket.git HEAD
					rtMaven.run pom: 'pom.xml', goals: "clean install", buildInfo: buildInfo
}		
			}
		}
		stage('Publish build info') {
			steps {
				script {
					server.publishBuildInfo buildInfo
				}
			}
		}
	}
}
