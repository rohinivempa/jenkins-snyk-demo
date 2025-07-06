pipeline {
    agent any
    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')  // Fetch Snyk token from Jenkins credentials
    }
    stages {
        stage('Code Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/vcjain/jenkins-snyk-demo.git']])
            }
		    
	    }
        stage('Snyk Security Scan manual') {
            steps {
                // sh '/usr/local/bin/snyk auth $SNYK_TOKEN'  // Authenticate Snyk
                // sh '/usr/local/bin/snyk test --json --severity-threshold=low'  // Run security scan
                // sh '/usr/local/bin/snyk monitor'  // Upload results to Snyk Dashboard
		 
            }
        }
	stage('Snyk Security Scan snippet') {
            steps {
                snykSecurity snykInstallation: 'snyk24'
            }
        }
    }
}
