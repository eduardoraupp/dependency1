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
					//def pomModel = readMavenPom
					//def pomVersion = pomModel.getVersion().replace(params.dependency1CurrentVersion, "0.0.2-SNAPSHOT")
					rtMaven.run pom: 'pom.xml' goals: 'scm:checkin -Dmessage="commiting the pom with the release version" -DpushChanges=false'
					rtMaven.run pom: 'pom.xml' goals: 'scm:tag -Dmessage="tag with release version"'
					rtMaven.run pom: 'pom.xml', goals: 'versions:set -DnewVersion="' + params.dependency1CurrentVersion + "'", buildInfo: buildInfo					
					//rtMaven.run pom: 'pom.xml' goals: 'versions:set -DnewVersion=${this.currentConfig[DEVELOPMENTVERSION].toString()}"
					rtMaven.run pom: 'pom.xml' goals: 'scm:checkin -Dmessage="updating pom" -DpushChanges'
											
					//rtMaven.run pom: 'pom.xml', goals: 'scm:checkin -DpushChanges'				
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
