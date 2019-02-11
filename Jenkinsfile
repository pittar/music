try {
    def appName=env.APP_NAME
    def gitSourceUrl=env.GIT_SOURCE_URL
    def gitSourceRef=env.GIT_SOURCE_REF
    def project=""
    node {
        stage("Initialize") {
            project = env.PROJECT_NAME
            echo "appName: ${appName}"
            echo "gitSourceUrl: ${gitSourceUrl}"
            echo "gitSourceUrl: ${gitSourceUrl}"
            echo "gitSourceRef: ${gitSourceRef}"
        }
    }
    node("jenkins-slave-gradle") {
        stage("Checkout") {
            git url: "${gitSourceUrl}", branch: "${gitSourceRef}"
        }
        stage("Build JAR") {
            sh "gradle build"
            sh "ls -ls build/libs"
            sh "cp build/libs/*.jar build/libs/app.jar"
            // sh "mvn sonar:sonar -Dsonar.host.url=http://sonarqube.cicd.svc:9000 -Dsonar.login=<generated token>"
            stash name:"jar", includes:"build/libs/app.jar"
        }
    }
    node {
        stage("Build Image") {
            unstash name:"jar"
            sh "oc start-build ${appName}-build --from-file=target/app.jar -n ${project}"
        }
    }
    /**
    node {
        stage("Deploy DEV") {
            openshift.withCluster() {
                openshift.withProject('cicd') {
                    openshift.tag("${appName}:latest", "${appName}:dev")
                }
            }
            openshift.withCluster() {
                openshift.withProject('petclinic-dev') {
                    def dc = openshift.selector('dc', "${appName}")
                    dc.rollout().status()
                }
            }
        }
    }
    node {
        stage("Deploy TEST") {
            input "Deploy to TEST?"
            openshift.withCluster() {
                openshift.withProject('cicd') {
                    openshift.tag("${appName}:dev", "${appName}:test")
                }
            }
            openshift.withCluster() {
                openshift.withProject('petclinic-test') {
                    def dc = openshift.selector('dc', "${appName}")
                    dc.rollout().status()
                }
            }
        }
    }
    */
} catch (err) {
    echo "in catch block"
    echo "Caught: ${err}"
    currentBuild.result = 'FAILURE'
    throw err
}
