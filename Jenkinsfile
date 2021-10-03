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
                    build job: 'test1', parameters: [string(name: 'Environment', value: "$env.Environment")]
                    echo "Starting test1"
                 }
/*                 steps {
                    build job: 'test2', parameters: [string(name: 'Environment', value: "$env.Environment")]
                    echo "Starting test2"
                 }*/
              }
              stage("API Tests"){
                 steps {
                    echo "Starting API tests"
                 }
              }
           }
        }
    }
}