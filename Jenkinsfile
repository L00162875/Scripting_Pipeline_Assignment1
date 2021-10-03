pipeline {
    agent any

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
/*                 steps {
                    build job: 'test2', parameters: [string(name: 'Environment', value: "$env.Environment")]
                    echo "Starting test2"
                 }*/
              }
              stage("API Tests"){
                 steps {
                    echo "Starting API tests"
                    build job: 'api_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                 }
              }
           }
        }
    }
}