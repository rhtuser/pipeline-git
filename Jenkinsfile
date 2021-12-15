pipeline {
    agent { label 'maven' }
	parameters {
		string(
			name: 'GIT_URL',
			defaultValue: 'https://github.com/rhtuser/DO400-apps.git'
		)
		string(
			name: 'SUBPROJECT',
			defaultValue: 'home-automation-service'
		)
		booleanParam(
			name: 'RUN_TESTS',
			defaultValue: true
		)
	}
	environment {
		PROJECT_NAME = 'Home Automation Service CI/CD Pipeline'
		WORKDIR = ${params.SUBPROJECT}
	}
    stages {
		stage('Say hello') {
			steps {
				echo "Welcome to build for ${env.PROJECT_NAME}!"
			}
		}
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
				cd ${WORKDIR}/
				./mvnw clean package -DskipTests -Dquarkus.package.type=uber-jar
				'''
				archiveArtifacts "${params.SUBPROJECT}/target/*-runner.jar"
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
						sh '''
						cd ${WORKDIR}/
						./mvnw test
						'''
					}
				}
				stage('Integration tests') {
					steps {
						echo "Running integration tests"
						sh '''
						cd ${WORKDIR}/
						./mvnw verify
						'''
					}
				}
			}
		}
		stage('Build container image') {
			environment {
				QUAY = credentials('quayregistry')
			}
			steps {
				echo 'Building container image with JIB'
				echo '(authenticating to quay.io as ${env.QUAY_USR})'
				// pushing to quay.io/rhtuser/home-automation-service:latest
				sh '''
				cd ${WORKDIR}/
				./mvnw quarkus:add-extension -Dextensions=container-image-jib
				./mvnw package \
					-Dquarkus.jib.base-jvm-image=registry.access.redhat.com/ubi8/openjdk-11:latest \
					-Dquarkus.container-image.build=true \
					-Dquarkus.container-image.push=true \
					-Dquarkus.container-image.registry=quay.io \
					-Dquarkus.container-image.group=rhtuser \
					-Dquarkus.container-image.name=home-automation-service \
					-Dquarkus.container-image.username=${QUAY_USR} \
					-Dquarkus.container-image.password=${QUAY_PSW}
				'''
			}
		}
    }
}
