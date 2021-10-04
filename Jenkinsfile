pipeline {
    agent any
    options {
        parallelsAlwaysFailFast()
    }

    stages {
        stage ('Build'){
           steps {
              echo "This is a build stage"
           }
        }

        stage('Tests'){
           parallel
           {
              stage("Unit Tests"){
                 steps {
                    echo "Starting unit_tests"
                    build job: 'unit_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                 }
              }
              stage("API Tests"){
                 steps {
                    echo "Starting API tests"
                    build job: 'api_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                 }
              }
           }
        }

        stage('Package'){
            steps {
                echo "This is a package stage"
            }
        }

        stage('Deploy'){
           when {
              not {
                 branch "master"
              }
           }
           steps{
              echo "Run if not master"
           }
        }
    }
}