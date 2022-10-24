def containerName="springbootdocker"
def tag="latest"
def dockerHubUser="vikasvikku"
def gitURL="https://github.com/vikasvikku/SpringBootDocker.git"

node {
	def sonarscanner = tool name: 'SonarQubeScanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    stage('Checkout') {
        checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/vikasvikku/SpringBootDocker.git' ]]]
    }

    stage('Build'){
        sh "mvn clean install"
    }

    stage("Image Prune"){
         sh "docker image prune -f"
    }

    stage('Image Build'){
        sh "docker build -t springbootdocker:latest --pull --no-cache ."
        echo "Image build complete"
    }

    stage('Push to Docker Registry'){
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'dockerUser', passwordVariable: 'dockerPassword')]) {
            sh "docker login -u vikasvikku -p Vikas@123"
            sh "docker tag springbootdocker:latest vikasvikku/springbootdocker:latest"
            sh "docker push vikasvikku/springbootdocker:latest"
            echo "Image push complete"
        }
    }
	
	stage("SonarQube Scan"){
        withSonarQubeEnv(credentialsId: 'SonarQubeToken') {
			sh "${sonarscanner}/bin/sonar-scanner"
		}
    }
	
	stage("Ansible Deploy"){
        ansiblePlaybook inventory: 'hosts', playbook: 'deploy.yaml'
    }
}
