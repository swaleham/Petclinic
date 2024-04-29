pipeline{
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout From Git'){
            steps{
                git branch: 'main', url: 'https://github.com/swaleham/Petclinic.git'
            }
        }
        stage('mvn compile'){
            steps{
                sh 'mvn clean compile'
            }
        }
        stage('mvn test'){
            steps{
                sh 'mvn test'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Petclinic '''
                }
            }
        }
        stage("Quality gate"){
           steps {
                 script {
                     waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                    }
                }
        }
        stage('mvn build'){
            steps{
                sh 'mvn clean install'
            }
        }
        stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ --format HTML ', odcInstallation: 'Dp-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.html'
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build -t petclinic22 ."
                       sh "docker tag petclinic22 swaleha6/pet-clinic123:latest "
                       sh "docker push swaleha6/pet-clinic123:latest "
                    }
                }
            }
        }
        stage("TRIVY"){
            steps{
                sh "trivy image swaleha6/pet-clinic123:latest > trivy.txt"
            }
        }
        stage('Clean up containers') { 
          steps {
           script {
             try {
                sh 'docker stop pet1'
                sh 'docker rm pet1'
                } catch (Exception e) {
                  echo "Container pet1 not found, moving to next stage"
                }
            }
          }
        }
        stage('Manual Approval') {
           timeout(time: 10, unit: 'MINUTES') {
           mail to: 'swaleha6375@gmail.com',
           subject: "${currentBuild.result} CI: ${env.JOB_NAME}",
           body: "Project: ${env.JOB_NAME}\nBuild Number: ${env.BUILD_NUMBER}\nGo to ${env.BUILD_URL} and approve deployment"
           input message: "Deploy ${params.project_name}?",
           id: "DeployGate",
           submitter: "approver",
           parameters: [choice(name: 'action', choices: ['Deploy'], description: 'Approve deployment')]
       }
        stage('Deploy to conatiner'){
            steps{
                sh 'docker run -d --name pet1 -p 8082:8080 swaleha6/pet-clinic123:latest'
            }
        }
        stage("Deploy To Tomcat"){
            steps{
                sh "sudo cp  /var/lib/jenkins/workspace/PetclinicCICD/target/petclinic.war /opt/apache-tomcat-9.0.65/webapps/ "
            }
        }
    }
    post {
     always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: "Project: ${env.JOB_NAME}<br/>" +
                "Build Number: ${env.BUILD_NUMBER}<br/>" +
                "URL: ${env.BUILD_URL}<br/>",
            to: 'swaleha6375@gmail.com',  
            attachmentsPattern: 'trivy.txt'
        }
    }
 }
}
