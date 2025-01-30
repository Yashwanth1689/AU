pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        // Local Deployment Path (Ensure this path is correct for your environment)
        LOCAL_DEPLOY_PATH = 'C:\\Jen'
    }

    stages {
        stage('Preparing') {
            steps {
                echo "Jenkins Home: ${env.JENKINS_HOME}"
                echo "Jenkins URL: ${env.JENKINS_URL}"
                echo "Jenkins JOB Number: {env.BUILD_NUMBER}"
                echo "Jenkins JOB Name: {env.JOB_NAME}"
                echo "GitHub BranchName: {env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building with workspace: ${WORKSPACE}"
                UiPathPack (
                    outputPath: "Output\\{env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.1"],
                    useOrchestrator: false,
                    traceLevel: 'None'
                )
            }
        }

        stage('Test') {
            steps {
                echo 'Testing the workflow...'
                // Add your testing commands here
            }
        }

        stage('Deploy to Local UAT') {
            steps {
                echo "Deploying ${env.BRANCH_NAME} to Local UAT at ${env.LOCAL_DEPLOY_PATH}\\UAT"
                bat """
                    if exist "${env.LOCAL_DEPLOY_PATH}\\UAT\\*" (
                        rmdir /S /Q "${env.LOCAL_DEPLOY_PATH}\\UAT"
                    )
                    mkdir "${env.LOCAL_DEPLOY_PATH}\\UAT"
                    xcopy /E /I /Y "Output\\${env.BUILD_NUMBER}\\*" "${env.LOCAL_DEPLOY_PATH}\\UAT\\"
                """
                // If using a Unix/Linux agent, replace the above `bat` block with:
                // sh """
                //     [ -d "{env.LOCAL_DEPLOY_PATH}/UAT" ] && rm -rf "${env.LOCAL_DEPLOY_PATH}/UAT/*"
                //     mkdir -p "{env.LOCAL_DEPLOY_PATH}/UAT"
                //     cp -r "Output/${env.BUILD_NUMBER}/." "${env.LOCAL_DEPLOY_PATH}/UAT/"
                // """
            }
        }

        stage('Deploy to Local Production') {
            steps {
                echo "Deploying ${env.BRANCH_NAME} to Local Production at ${env.LOCAL_DEPLOY_PATH}\\Production"
                bat """
                    if exist "${env.LOCAL_DEPLOY_PATH}\\Production\\*" (
                        rmdir /S /Q "${env.LOCAL_DEPLOY_PATH}\\Production"
                    )
                    mkdir "${env.LOCAL_DEPLOY_PATH}\\Production"
                    xcopy /E /I /Y "Output\\${env.BUILD_NUMBER}\\*" "${env.LOCAL_DEPLOY_PATH}\\Production\\"
                """
                // If using a Unix/Linux agent, replace the above `bat` block with:
                // sh """
                //     [ -d "${env.LOCAL_DEPLOY_PATH}/Production" ] && rm -rf "${env.LOCAL_DEPLOY_PATH}/Production/*"
                //     mkdir -p "${env.LOCAL_DEPLOY_PATH}/Production"
                //     cp -r "Output/${env.BUILD_NUMBER}/." "${env.LOCAL_DEPLOY_PATH}/Production/"
                // """
            }
        }
    }

    options {
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            echo 'Deployment has been completed successfully!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        always {
            // Uncomment the next line if you want to clean the workspace after every run
            // cleanWs()
            echo 'This will always run regardless of the pipeline result.'
        }
    }
