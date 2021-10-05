pipeline {
    agent any
    options {
        parallelsAlwaysFailFast()
    }
    environment {
        TS = ts()
    }
    stages {
        stage ('Build'){
           steps {
              echo "Building timestamp ${TS}"
              sh 'mvn -B -DskipTests clean'
           }
        }

        stage('Tests'){
           parallel
           {
              stage("Unit Tests"){
                 steps {
                    echo "Starting unit_tests for build timestamp ${TS}"
//                     build job: 'unit_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                    sh 'mvn test'
                 }
                 post {
                     always {
                         junit 'target/surefire-reports/*.xml'
                     }
                 }
              }
              stage("API Tests"){
                 steps {
                    echo "Starting API tests for build timestamp ${TS}. This is to demonstrate parallel optimization"
//                     build job: 'api_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                    sh 'mvn verify'
                 }
              }
           }
        }

        stage('Package'){
            steps {
                echo "Packaging build for timestamp ${TS}"
                sh 'mvn package -DskipTests=true'
            }
        }

        stage('Deploy main development pipeline'){
           when {
             branch "main"
           }
           steps{
              echo "Deploying main development pipeline"
              package("main")
           }
        }

        stage('Deploy staging pipeline'){
           when {
             branch "staging"
           }
           steps{
              echo "Deploying staging pipeline"
              package("staging")
           }
        }

        stage('Deploy production pipeline'){
           when {
             branch "production"
           }
           steps{
              echo "Deploying production pipeline"
              package("production")
           }
        }
    }

    post {
       success {
          mail to: team@student.lyit.com, subject: 'The pipeline success'
       }
    }
}

def ts() {
    def string_timestamp_var = "the current time is:"
    echo "${string_timestamp_var}"
    def time = new Date()
    string_timestamp = time.format("yyMMdd.HHmm.ss", TimeZone.getTimeZone('UTC'))
    sh """
    echo ${string_timestamp}
    """
    return string_timestamp
}

def package(branchName) {
    def projectName = sh (
        script: 'mvn help:evaluate -Dexpression=project.name | grep "^[^\\[]"',
        returnStdout: true
    ).trim()
    echo "Project name: ${projectName}"
    def projectVersion = sh (
        script: 'mvn help:evaluate -Dexpression=project.version | grep "^[^\\[]"',
        returnStdout: true
    ).trim()
    echo "Project version: ${projectVersion}"
    def fileName = projectName + '-' + projectVersion
    def jarPath = "target/${fileName}.jar"

    def artifactName = projectName + '-' + projectVersion + '-' + branchName + '-' + TS
    def artifactPath = "target/${artifactName}.jar"
    echo "Going to exec: mv ${jarPath} ${artifactPath}"
    sh "mv ${jarPath} ${artifactPath}"
    echo "Final artifact for branch ${branchName} is ready: ${artifactPath}"
}