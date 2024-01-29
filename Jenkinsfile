pipeline {
    // agent any
    agent {
        // choose an agent where docker is installed
        label 'scan-rl-secure-dev'
    }

    environment {
        ARTIFACT2SCAN="struts2-showcase.war"
    }

    stages {

        stage('# Build') {
            steps {
                // our runner has no withMaven plugin, but we have maven local installed
                // withMaven( maven: 'mvn3' ) {
                    sh 'mvn clean package'
                // }
            }
        }

        stage('# Test rl-secure') {
            environment {
                RLPORTAL_ACCESS_TOKEN = credentials('mboot-test-RLPORTAL_ACCESS_TOKEN')
                RLPORTAL_SERVER="test"
                RLPORTAL_ORG="Test"
                RLPORTAL_GROUP="Default"
            }

            steps {
                sh '''
                    # dont let docker create directory's they will be created root:root
                    # if the jenkins job creates them,
                    # they will be the user you expect (e.g. jenkins:jenkins)
                    mkdir -p "${WORKSPACE}/RLreport/${BUILD_NUMBER}"

                    docker run --rm -u \$(id -u):\$(id -g) \
                        -e RLPORTAL_ACCESS_TOKEN="${RLPORTAL_ACCESS_TOKEN}" \
                        -v "${WORKSPACE}/target:/packages:ro" \
                        -v "${WORKSPACE}/RLreport/${BUILD_NUMBER}:/report" \
                        reversinglabs/rl-scanner-cloud:latest \
                            rl-scan \
                                --replace \
                                --rl-portal-server "${RLPORTAL_SERVER}" \
                                --rl-portal-org "${RLPORTAL_ORG}" \
                                --rl-portal-group "${RLPORTAL_GROUP}" \
                                --purl "test-project/struts2-showcase@v2.5.28" \
                                --file-path "/packages/${ARTIFACT2SCAN}" \
                                --report-path=/report \
                                --report-format=all
                '''
            }
        }
    }

    post {
        always {
            sh '''
                # delete first: so you never look at possible data from a earlier run
                rm -rf RLreport/latest &&
                mkdir -p RLreport/latest &&
                cp -r RLreport/$BUILD_NUMBER/* RLreport/latest/
            '''

            archiveArtifacts artifacts: 'RLreport/latest/*.json',
                onlyIfSuccessful: false

            // note: there is currently no html report
        }
    }
}
