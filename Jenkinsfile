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
              echo "This is a build stage ${TS}"
           }
        }

        stage('Tests'){
           parallel
           {
              stage("Unit Tests"){
                 steps {
                    echo "Starting unit_tests ${TS}"
                    build job: 'unit_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                 }
              }
              stage("API Tests"){
                 steps {
                    echo "Starting API tests ${TS}"
                    build job: 'api_tests', parameters: [string(name: 'Environment', value: "$env.Environment")]
                 }
              }
           }
        }

        stage('Package'){
            steps {
                echo "This is a package stage ${TS}"
            }
        }

        stage('Deploy'){
           when {
              not {
                 branch "master"
              }
           }
           steps{
              echo "--"
           }
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
