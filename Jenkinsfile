pipeline {
    agent { label 'maven' }
    stages {
	stage('Clone source code') {
	    steps {
		echo 'Cloning source code from https://github.com/rhtuser/counter.git'
		git 'https://github.com/rhtuser/counter.git'
	    }
	}
    }
}
