pipeline {
	agent any
	tools { nodejs 'node22' }

	environment {
		NPM_TOKEN = credentials('lartiorkor-npm-token')
		GITHUB_TOKEN = credentials('lartiorkor-gh-token')
	}

	stages {
		stage('Install Dependencies') {
			steps {
				sh 'npm i'
			}
		}

		stage('Build') {
			steps {
				sh 'npm run build'
			}
		}

		stage('Publish To NPM') {
			when {
				branch 'dummy'
			}

			steps {
				sh 'git config user.email "$(git log -n 1 --pretty=format:%ae)"'
				sh 'git config user.name "$(git log -n 1 --pretty=format:%an)"'
			
				withCredentials([usernamePassword(credentialsId: 'lartiorkor-gh-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
					
					sh 'git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/devOps-task-2.git"'
					sh 'git push origin HEAD:dummy'
				}
				sh 'npx semantic-release'
			}
		}
	}

	post {
		always {
			cleanWs()
		}
	}
}