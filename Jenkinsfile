def jobnameparts = JOB_NAME.tokenize('/') as String[]
def repositoryname = jobnameparts[1]

pipeline {	
    agent {
        label 'master'
    }
	options {        
		skipDefaultCheckout() // Don't checkout automatically
	}

	stages {

		stage('Checkout') {
					
			steps {
    			script {
    				def scmVars = checkout scm
    				git "$scmVars.GIT_URL"
    				}    			
    		}
    	}
    
    	stage('Build') {				
			// build steps here

			steps {
				
				script {
				
				  if (env.BRANCH_NAME == 'develop' || env.BRANCH_NAME.startsWith('feature') || env.BRANCH_NAME.startsWith('jenkins')) {
					echo "Running a snapshot build from branch ${env.BRANCH_NAME}"
					def mvnHome = tool name: 'M3', type: 'maven'
					def mvnCMD = "${mvnHome}/bin/mvn"
					sh "${mvnCMD} package -e"
				  }	
				  if (env.BRANCH_NAME.startsWith('release') || env.BRANCH_NAME == 'master' ) {
				  	echo "Running a release build from branch ${env.BRANCH_NAME}"
					sh 'mvn install'
				  }
				  
				}	  
			}
		}
	
	}
}
