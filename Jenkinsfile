pipeline {
	agent any
	stages {
		stage("Build") {
			steps {
				script {
					if(params.isParentRelease || (params.parentDependencyVersion != null && params.parentDependencyVersion != "X.X.X")) {
		
						sh "mvn scm:checkin -Dmessage=\"commiting the pom with the release version\" -DpushChanges=false"
						sh 'mvn versions:set-property -Dproperty=\"independent\" -DnewVersion="' + params.parentDependencyVersion + '"'
						sh "mvn scm:checkin -Dmessage=\"updating pom\" -DpushChanges"
						sh 'mvn -B release:clean release:prepare release:perform'
						sh 'mvn versions:set-property -Dproperty=\"independent\" -DnewVersion="' + params.parentDependencyVersion + '-SNAPSHOT"'
						sh "mvn scm:checkin -Dmessage=\"updating pom\" -DpushChanges"
					}
							//git ls-remote -h git@bitbucket.org:person/projectmarket.git HEAD
				}		
			}
		}
	}
}
