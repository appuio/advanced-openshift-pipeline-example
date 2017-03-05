// define Logrotation and scm Polling
properties(
    [
        [
            $class: 'BuildDiscarderProperty',
            strategy: [$class: 'LogRotator', numToKeepStr: '10']
        ],
        pipelineTriggers([cron('H/5 * * * *')]),
    ]
)

try{
    node('nodejs') {

        stage('Checkout'){
            checkout scm
        }
        // run test in Jenkins Slave
        stage('buildAndTest'){
             sh "npm build"
        }

        // trigger Build on OpenShift
        stage('buildOnOpenShift'){
             openshiftBuild(buildConfig: '${NAME}', showBuildLogs: 'true')
        }

        stage('deploy') {
            openshiftDeploy(deploymentConfig: '${NAME}')
        }
    }
} catch (Exception e) {
    // Notify
    echo "send error mail to jenkins@appuio.ch"
    mail subject: "Build failed with ${e.message}", to: "jenkins@appuio.ch", body: "Job failed: ${env.BUILD_URL} \n\n${e.stackTrace}"
    throw e;
}
