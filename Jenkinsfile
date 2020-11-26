#!groovy
import hudson.model.*
import java.time.*
import java.time.format.DateTimeFormatter

pipeline {
    agent any

    stages {
        stage('Build Environment') {
            steps {
                script {
                    try {
                        echo "GIT_REPO :: $GIT_REPO"
                        echo "JOB_BASE_NAME :: $JOB_BASE_NAME"
                        echo "JOB_NAME :: $JOB_NAME"
                        echo "BUILD_URL :: $BUILD_URL"
                        echo "WORKSPACE :: $WORKSPACE"
                        echo "IMAGE_TAG :: $IMAGE_TAG"
                        echo "GIT_BRANCH :: $GIT_BRANCH"
                    } catch (err) {
                        echo "Some variables not set"
                    }

                }
            }
        }
        
        stage('Checkout') {
            steps {
                dir('build') {
                    checkout([$class: 'GitSCM', 
                        branches: [[name: "master"]], 
                        doGenerateSubmoduleConfigurations: false, 
                        extensions: [[$class: 'CleanCheckout']], 
                        submoduleCfg: [],
                        browser: [$class: 'GithubWeb', repoUrl: '${GIT_REPO}'],
                        userRemoteConfigs: [[credentialsId: 'github-app-test', url: "${GIT_REPO}"]]])
                }
                publishChecks name: 'example', title: 'Pipeline Check', summary: 'check through pipeline', text: 'you can publish checks in pipeline script', detailsURL: 'https://github.com/jenkinsci/checks-api-plugin#pipeline-usa'
            }
        }
    }
}


void sendMail(String mailSubject, String recipientProviders) {
    
    wrap([$class: 'BuildUser']) {
        if (env.BUILD_USER_ID == null) {
            env.BUILD_USER_ID = env.gitlabUserName
        }
        
        emailext(
            body: """
            <p>
            Dear ${env.BUILD_USER_ID},  </br>
            </br>
            Project: <a href="${gitRepo}">${gitRepo}</a> </br>
            Git Commit: ${GIT_COMMIT} </br>
            Branch: ${env.gitlabBranch} </br>
            Pipeline <a href='${env.BUILD_URL}'>${env.JOB_NAME} » ${env.BUILD_NUMBER}</a> had 1 failed build. </br>
            </p>
            """,
            mimeType: 'text/html',
            subject: mailSubject,
            recipientProviders: [[$class: recipientProviders]],
            to: "$MAIL_RECEIVERS"
        )
    }
}
