pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    environment {
        // You need to specify 4 required environment variables first, they are going to be used for the following IBM Cloud DevOps steps
        IBM_CLOUD_DEVOPS_API_KEY = credentials('IBM_CLOUD_DEVOPS_API_KEY')
        IBM_CLOUD_DEVOPS_ORG = 'sikkar@us.ibm.com'
        IBM_CLOUD_DEVOPS_APP_NAME = 'simple-java-maven-app'
        IBM_CLOUD_DEVOPS_TOOLCHAIN_ID = '91c65998-025d-4882-b877-a4404dd3ef10'
    }
    stages {
        stage('Build') {
            environment {
                // get git commit from Jenkins
                GIT_COMMIT = 'dummy commit' //sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                GIT_BRANCH = 'master'
                GIT_REPO = 'https://github.com/jenkins-docs/simple-java-maven-app/'
            } 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
            // post build section to use "publishBuildRecord" method to publish build record
            post {
                success {
                    publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"SUCCESS"
                }
                failure {
                    publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"FAIL"
                }
            }
        }
	stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    publishTestResult type:'unittest', fileLocation: './maventest.json'
                }
            }
        }
	stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
