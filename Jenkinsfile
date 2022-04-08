pipeline {
    // The “agent” section configures on which nodes the pipeline can be run.
    // Specifying “agent any” means that Jenkins will run the job on any of the
    // available nodes.
		agent any
    stages {
        stage('Git Pull') {
            steps {
                // Get code from a GitHub repository
                // Make sure to add your own git url and credentialsId
				git url: 'https://github.com/shwetanknaveen/CalculatorMiniProject.git',
				branch: 'master',
                credentialsId: 'github'
            }
        }
        stage('Maven Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Build Docker Images') {
            steps {
                sh 'docker build -t shwetanknaveen/spe_mini_project:latest .'
            }
        }
        stage('Publish Docker Images') {
            steps {
                withDockerRegistry([ credentialsId: "dockerhub", url: "" ]) {
                    sh 'docker push shwetanknaveen/spe_mini_project:latest'
                }
            }
        }
        //cleaning docker images from local jenkins workspace because it is not needed here and will consume space uneccessarily
        stage('Clean Docker Images') {
            steps {
                sh 'docker rmi -f shwetanknaveen/spe_mini_project'
            }
        }
        stage('Deploy and Run Image'){
            steps {
                ansiblePlaybook becomeUser: null, colorized: true, disableHostKeyChecking: true, installation: 'Ansible', inventory: 'inventory', playbook: 'p2.yml', sudoUser: null
            }
        }
        // stage('Attach docker container') {//for trying automatic opening of terminal to show o/p of jar execution
        //     steps {

        //     }
        // }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}