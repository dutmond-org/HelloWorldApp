def jobnameparts = JOB_NAME.tokenize('/') as String[]
def repositoryname = jobnameparts[2]

pipeline {	
	
	agent {
		label "TestApp"
	}
	options {        
		skipDefaultCheckout() // Don't checkout automatically
	}

	stages {

		stage('Checkout') {
					
			steps {
				dir('commercial-build-utils') {
    				git branch: 'jenkins-poc', credentialsId: 'Chris-Petrina', url: 'https://github.info53.com/Fifth-Third/commercial-build-utils.git'
    			}
    			dir("${repositoryname}") {
    			script {
    				def scmVars = checkout scm
    				git branch: "$scmVars.GIT_BRANCH", credentialsId: 'Chris-Petrina', url: "$scmVars.GIT_URL"
    				}
    			}
    		}
    	}
    
    	stage('Build') {				
			// build steps here

			environment {
    			build = "..\\\\..\\\\commercial-build-utils\\\\build"
 			}
			steps {
				
				script {
				
				  if (env.BRANCH_NAME == 'develop' || env.BRANCH_NAME.startsWith('feature') || env.BRANCH_NAME.startsWith('jenkins')) {
					echo "Running a snapshot build from branch ${env.BRANCH_NAME}"
					bat '''
						for /f "tokens=1,2,3,4 delims=/" %%a in ("%JOB_NAME%") do set lob=%%a&set gitorg=%%b&set reponame=%%c&set gitbranch=%%d
						echo "Github Repository name is %reponame%"
				  		cd %reponame%\\build
						ant -Dbuild-utils-dir=%build% clean-cache publish-snapshot-jar-artifact clean-cache
					'''	
				  }	
				  if (env.BRANCH_NAME.startsWith('release') || env.BRANCH_NAME == 'develop' ) {
				  	echo "Running a release build from branch ${env.BRANCH_NAME}"
					bat '''
						for /f "tokens=1,2,3,4 delims=/" %%a in ("%JOB_NAME%") do set lob=%%a&set gitorg=%%b&set reponame=%%c&set gitbranch=%%d
						echo "Github Repository name is %reponame%"
				  		cd %reponame%\\build
						ant -Dbuild-utils-dir=%build% clean-cache createJarArtifact commit-artifacts clean-cache
					'''	
				  }
				  
				}	  
			}
		}
	
    	stage('Publish to Jfrog') {
    		steps {
    			echo 'publishing artifacts to jfrog'
    		  script {
    		  	def scmVars = checkout scm
    			def server = Artifactory.server 'jfrog-artifactory'    			
    			def uploadSpec = """{
  					"files": [
    					{
      						"pattern": "${repositoryname}/build/${repositoryname}-build/artifacts/*.jar",
      						"target": "ivy-sandbox-local/com.fifththird.${repositoryname}/${scmVars.GIT_COMMIT}/"
   						}
 					]
				}"""
				server.upload(uploadSpec)
			  }
    		}
		}
	}
}
