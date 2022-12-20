def BRANCH_NAME = scm.branches[0].name
if (BRANCH_NAME.contains("*/")) {
    BRANCH_NAME = BRANCH_NAME.split("\\*/")[1]
    }

if (BRANCH_NAME == 'master') {
	ENV_NAME = 'prod'
} else if (BRANCH_NAME == 'qa') {
	ENV_NAME = 'staging'
} else {
	ENV_NAME = 'dev'
}

pipeline {
	agent any
	tools {
		// Install the Maven version configured as "M3" and add it to the path.
		maven "apache-maven-3.8.6"
	}
	stages {
        stage('Build Application') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                echo 'Application in Testing Phase…'
                sh 'mvn test'
            }
        
        }
        stage('Deploy CloudHub') {
            environment {
                ANYPOINT_CREDENTIALS = credentials('anypointPlatform')
            }
            steps {
                echo 'Deploying mule project due to the latest code commit…'
                echo 'Deploying to the configured environment….' + BRANCH_NAME
                sh 'mvn deploy -DmuleDeploy -Dusername=${ANYPOINT_CREDENTIALS_USR} -Dpassword=${ANYPOINT_CREDENTIALS_PSW} -DworkerType=Micro -Dworkers=1 -Dregion=us-west-2 -Denv=${ENV_NAME}'
            }
        }
	}
}
