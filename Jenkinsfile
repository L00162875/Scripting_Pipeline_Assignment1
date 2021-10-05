def skipStaging = false
def TS

node('master') {
    properties(
        [
            parallelsAlwaysFailFast()
        ]
    )
    try {
        stage ('Build'){
            TS = ts()
            echo "Building timestamp ${TS}"
            sh 'mvn -B -DskipTests=true clean'
        }

        stage ('Tests') {
            parallel([
                unit_tests: {
                    echo "Starting unit_tests for build timestamp ${TS}"
                    sh 'mvn test'
                },
                api_tests: {
                    echo "Starting API tests for build timestamp ${TS}. This is to demonstrate parallel optimization"
                    sh 'mvn verify'
                }
            ])
        }

        stage('Package') {
            echo "Packaging build for timestamp ${TS}"
            sh 'mvn package -DskipTests=true'
        }

        stage('Deploy main development pipeline') {
            if (env.BRANCH_NAME == 'main') {
                echo "Deploying main development pipeline"
                buildPackage("main")
                echo "Programmatically disabling staging for demonstration purposes"
                if (true) {
                    skipStaging = true
                }
            }
        }

        stage('Deploy staging pipeline') {
            if (env.BRANCH_NAME == 'staging' && !skipStaging) {
              echo "Deploying staging pipeline"
              buildPackage("staging")
           }
        }

        stage('Deploy production pipeline') {
            if (env.BRANCH_NAME == 'production') {
              echo "Deploying production pipeline"
              buildPackage("production")
            }
        }
    } catch (e) {
        echo "Error"
        throw e
    } finally {
        echo "Do something on success"
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

def buildPackage(branchName) {
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
    echo "Going to exec: cp ${jarPath} ${artifactPath}"
    sh "cp ${jarPath} ${artifactPath}"
    echo "Final artifact for branch ${branchName} is ready: ${artifactPath}"
}