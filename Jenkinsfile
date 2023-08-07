pipeline {
  agent any

  environment {
    PATH = "/usr/local/go/bin:${env.PATH}"
  }

  stages {
    stage('Check Requirements') {
      steps {
        echo 'Pulling... ' + env.GIT_BRANCH

        script {
          // Retrieve the last commit message using git command
          env.GIT_COMMIT_MSG = sh(script: 'git log -1 --pretty=%B ${GIT_COMMIT}', returnStdout: true).trim()
        }

        echo "${GIT_COMMIT_MSG}"
        sh 'whoami'
        echo 'Installing dependencies'
        sh 'which go'
        sh 'go version'
      }
    }

    stage('Run Test') {
      steps {
        echo 'Belum Ada Test silahkan lanjut'
      }
    }

    stage('Build Binary') {
      steps {
        echo 'Compiling and building Binary'

        sh 'CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags="-s -w" -o rest-api main.go'
        sh 'chmod +x rest-api'
        sh 'chmod +x script_dev.sh'
      }
    }

    stage('Deploy Server Development') {
      when {
        expression {
          return env.GIT_BRANCH == 'origin/dev';
        }
      }
      steps {
        sh 'whoami'
        sh 'mv script_dev.sh /root/rest-api/script_dev.sh'
        sh './script_dev.sh'
      }
    }

    stage('Deploy Server Production') {
      when {
        expression {
          return env.GIT_BRANCH == 'origin/main';
        }
      }
      steps {
        sh "ssh root@167.71.206.43 'sudo fuser -n tcp -k 54321'"
        sh 'scp rest-api root@167.71.206.43:/root/rest-api/'
        sh "ssh root@167.71.206.43 'sudo /root/rest-api/run.sh'"

      }
    }

  }
}