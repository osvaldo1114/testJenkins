def branchName = scm.branches[0].name
if (branchName.contains("*/")) {
    branchName = branchName.split("\\*/")[1]
    }

def envName = 'dev'

if (branchName == 'master') {
	envName = 'prod'
} else if (branchName == 'qa') {
	envName = 'staging'
} else {
	envName = 'dev'
}

pipeline {
	agent any
	tools {
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
        stage('Deploy to ARM') {
            environment {
                ANYPOINT_CREDENTIALS = credentials('anypointPlatform')
            }
            steps {
            	withCredentials([
            		string(credentialsId: 'server_name_${envName}', variable: 'SERVER_NAME'),
            		string(credentialsId: 'server_type_${envName}', variable: 'SERVER_TYPE')
            	]){
                	echo 'Deploying mule project due to the latest code commit…'
                	echo 'Environment: ' + envName
                	echo 'Server Name: ' + SERVER_NAME
                	echo 'Server Type: ' + SERVER_TYPE
                	sh 'mvn deploy -DmuleDeploy -Dusername=${ANYPOINT_CREDENTIALS_USR} -Dpassword=${ANYPOINT_CREDENTIALS_PSW} -Dtarget=${SERVER_NAME} -Dtarget.type=${SERVER_TYPE} -Denv=' + envName
                }
            }
        }
	}
}
