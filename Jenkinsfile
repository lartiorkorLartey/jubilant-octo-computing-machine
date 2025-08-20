pipeline {
  agent any

  tools { nodejs 'node22' }

  environment {
    NPM_TOKEN = credentials('lartiorkor-npm-token')
    GITHUB_TOKEN = credentials('lartiorkor-gh-token')
  }

  stages {
    stage('Checkout') {
        steps {
            scmSkip(deleteBuild: true, skipPattern:'.*\\[ci skip\\].*')
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
        branch 'dummy'
      }

      steps {
        // sh "echo '//registry.npmjs.org/:_authToken=\${NPM_TOKEN}' > .npmrc"
        // sh 'npm publish --access public'
        // sh 'rm .npmrc'

        sh 'git config user.email "$(git log -n 1 --pretty=format:%ae)"'
        sh 'git config user.name "$(git log -n 1 --pretty=format:%an)"'

        withCredentials([usernamePassword(credentialsId: 'lartiorkor-gh-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
          sh 'git remote set-url origin "https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/jubilant-octo-computing-machine.git"'
        //   sh 'git push --follow-tags'
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
