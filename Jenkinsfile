pipeline {
	agent any
	tools { nodejs 'node22' }

	environment {
		NPM_TOKEN = credentials('lartiorkor-npm-token')
		GITHUB_TOKEN = credentials('lartiorkor-gh-token')
	}

	stages {
		stage('Checkout Main') {
			steps {
				checkout([
					$class: 'GitSCM',
					branches: [[name: "*/main"]],
					doGenerateSubmoduleConfigurations: false,
					extensions: [],
					userRemoteConfigs: [[
						url: "https://github.com/Amali-Tech/unified-experience.git",
						credentialsId: "lartiorkor-gh-creds"
					]]
				])
			}
		}

		stage('Install Dependencies') {
			steps {
				sh 'npm ci'
			}
		}

		stage('Build') {
			steps {
				sh 'npm run build'
			}
		}

		stage('Publish To NPM') {
			when {
				branch 'main'
			}

			steps {
				sh 'git config user.email "$(git log -n 1 --pretty=format:%ae)"'
				sh 'git config user.name "$(git log -n 1 --pretty=format:%an)"'
			
				// withCredentials([usernamePassword(credentialsId: 'lartiorkor-gh-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
					
				// 	sh 'git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/jubilant-octo-computing-machine.git"'
				// 	// sh 'git push origin HEAD:dummy'
                //     // sh 'git push --follow-tags'



				// }
				// sh 'npx semantic-release'

			withCredentials([usernamePassword(credentialsId: 'lartiorkor-gh-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
			sh '''
				git config user.email "$(git log -n 1 --pretty=format:%ae)"
				git config user.name "$(git log -n 1 --pretty=format:%an)" 
				
				git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/Amali-Tech/unified-experience.git"

				echo "================= GIT DIAGNOSTICS ================="

				echo ">>> Remote configuration:"
				git remote -v
				git remote show origin || true

				echo ">>> Current branch & upstream:"
				git status -vv

				echo ">>> Branch list with tracking info:"
				git branch -vv

				echo ">>> Last 5 local commits (main):"
				git log main -5 --oneline || true

				echo ">>> Last 5 remote commits (origin/main):"
				git log origin/main -5 --oneline || true

				echo ">>> Local commits missing from remote (main..origin/main):"
				git log main..origin/main --oneline || echo "None"

				echo ">>> Remote commits missing locally (origin/main..main):"
				git log origin/main..main --oneline || echo "None"

				echo ">>> Tags (local vs remote):"
				git tag --list | tail -n 5
				git ls-remote --tags origin | tail -n 5

				echo ">>> Default branch reported by remote:"
				git remote show origin | grep 'HEAD branch' || true

				echo "=================================================="
				
				npx semantic-release
			'''				
			}
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
