/**
*/
node {
  echo sh(script: 'env|sort', returnStdout: true)
  checkout scm
  docker.image('generalmeow/jenkins-tools:1.6-arm')
        .inside('-v /home/paul/work/docker/docker-maven-repo:/root/.m2/repository') {

    stage ('Initialize') {
      //sh '''
      //'''
    }
    stage('Static analysis') {
      echo 'Running static analysis tools..'
      // sh 'mvn clean verify -P check -DskipTests'
    }
    stage('Build') {
      echo 'Building..'
      sh 'mvn clean compile'
    }
    stage('Test') {
      echo 'Testing..'
      sh 'mvn test'
    }
    stage('Maven Install') {
      echo 'Installing artifact locally'
      sh 'mvn install -DskipTests'
    }
    stage('Deploy jar to nexus') {
      echo 'Deploying Jar to Nexus....'
      sh 'mvn deploy -DskipTests'
    }
    stage('Build and Publish Docker Image') {
      echo 'downloading artifacts from nexus....'
      pom = readMavenPom file: 'pom.xml'

      def pomVersion = pom.version
      def projectName = 'reborn-config-service'

      sh 'mkdir -p ./downloads'
      //server.download(downloadSpec)
      sh 'curl -o ./downloads/app.jar "http://tinker.paulhoang.com:8081/repository/maven-releases/com/paulhoang/${projectName}/${pomVersion}/${projectName}-${pomVersion}.jar"'
      echo 'Download comeplete'

      echo 'Building docker image....'
      def dockerImage = docker.build("generalmeow/${projectName}:${env.BUILD_ID}", ".")

      echo 'Pushing Docker Image....'
      docker.withRegistry('https://registry.hub.docker.com', 'generalmeow-dockerhub'){
        dockerImage.push()
      }
    }
    stage('Deploy Docker Image') {
      echo 'Deploying Docker Image....'
    }
  }
}
