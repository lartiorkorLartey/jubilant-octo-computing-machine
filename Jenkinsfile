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
					
					sh 'git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/jubilant-octo-computing-machine.git"'
					sh 'git push origin HEAD:dummy'
                    // sh 'git push --follow-tags'
				}
				sh 'npx semantic-release'

				// sh "echo '//registry.npmjs.org/:_authToken=\${NPM_TOKEN}' > .npmrc"
				// sh 'npm publish --access public'
				// sh 'rm .npmrc'
			}
		}
	}

	post {
		always {
			cleanWs()
		}
	}
}
