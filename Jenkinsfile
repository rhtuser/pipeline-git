pipeline {
    agent { label 'maven' }
	parameters {
		string(
			name: 'GIT_URL',
			defaultValue: 'https://github.com/rhtuser/DO400-apps.git'
		)
		booleanParam(
			name: 'RUN_TESTS',
			defaultValue: true
		)
	}
    stages {
		stage('Clone source code') {
			steps {
				echo "Cloning source code from ${params.GIT_URL}"
				git "${params.GIT_URL}"
			}
		}
		stage('Perform build') {
			steps {
				echo "Rebuilding the application."
				sh '''
				cd home-automation-service/
				./mvnw clean package -DskipTests -Dquarkus.package.type=uber-jar
				'''
				archiveArtifacts 'home-automation-service/target/*-runner.jar'
			}
		}
		stage('Run tests') {
			when {
				expression { params.RUN_TESTS == true }
			}
			parallel {
				stage('Unit tests') {
					steps {
						echo "Running unit tests"
					}
				}
				stage('Integration tests') {
					steps {
						echo "Running integration tests"
					}
				}
			}
		}
    }
}
