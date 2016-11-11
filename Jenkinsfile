def dockerBuildTag = 'latest'
def buildVersion = null
def mobileDepositUiImage = null
stage 'build'
node('docker-cloud') {
  docker.image('kmadel/maven:3.3.3-jdk-8').inside() { //use this image as the build environment
    //checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/cloudbees/mobile-deposit-ui.git']]])
    checkout scm
    sh 'mvn -Dmaven.repo.local=/data/mvn/repo clean package'
    stash name: 'pom', includes: 'pom.xml'
    stash name: 'war-dockerfile', includes: '**/target/*.war,**/target/Dockerfile'

    //get new version of application from pom
    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
    if (matcher) {
        buildVersion = matcher[0][1]
        echo "Released version ${buildVersion}"
    }
    matcher = null
  }
}


//build image and deploy to staging
stage 'build docker image'
node('docker-cloud') {
  unstash 'jar-dockerfile'
  unstash 'pom'
  dir('target') {
    mobileDepositUiImage = docker.build "bank-deposit-ui:${buildVersion}"
  }
  try {
    sh "docker stop bank-deposit-ui-stage"
    sh "docker rm bank-deposit-ui-stage"
  } catch (Exception _) {
      echo "no container to stop"
    }
}

stage 'deploy to QA'
node('docker-cloud') {
  //mobileDepositUiImage.run("--name bank-deposit-ui-stage -p 82:8080 --env='constraint:node==bankdemo-swarm-master'")
  echo "QA Test successful"
}

input 'UI Staged at URL - Proceed with Production Deployment?'
stage 'deploy to production'
node('docker-cloud') {
  try {
    sh "docker stop bank-deposit-ui"
    sh "docker rm bank-deposit-ui"
  } catch (Exception _) {
      echo "no container to stop"
    }
  echo "deployed service in PROD"
  //mobileDepositUiImage.run("--name bank-deposit-ui -p 80:8080 --env='constraint:node==bankdemo-swarm-master'")
  //sh 'curl http://webhook:58f11cf04cecbe5633031217794eda89@jenkins-job-url/submitContainerStatus --data-urlencode inspectData="$(docker inspect bank-deposit-ui)"'
}
