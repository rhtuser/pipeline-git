pipeline {
    agent { label 'maven' }
	parameters {
		string(
			name: 'GIT_URL',
			defaultValue: 'https://github.com/rhtuser/counter.git'
		),
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
			input {
				message "Do you want to rebuild?"
			}
			steps {
				echo "Rebuilding the application."
			}
		}
		stage('Run tests') {
			when {
				params.RUN_TESTS
			}
			steps {
				echo "Running tests"
			}
		}
    }
}
