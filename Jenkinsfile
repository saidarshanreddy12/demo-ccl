#!groovy


pipeline {
    agent any
    tools {
        maven 'Maven'
        nodejs 'NodeJS'
    }
    stages {
        stage('Clean') {
            steps {
                dir('edge') {
                    sh "mvn clean"
                }
            }
        }
        stage('Linting') {
            steps {
                dir("edge") {
                   /* sh '''
                        cp -R /tmp/APIGEE/APIGEELINT/. /var/lib/cloudbees-core-cm/workspace/APIGEE/Basic-Template/edge
                        mvn package
                        npmbin=`npm bin`
                        eval $npmbin/apigeelint -s apiproxy/ -f html.js > target/apigeelint
                        '''*/
                     sh "npm install"
                     sh  "mvn package"
                    sh "apigeelint -s apiproxy -f html.js > target/apigeelint"
                    echo "publish html report"
                    publishHTML(target: [
                            allowMissing         : false,
                            alwaysLinkToLastBuild: false,
                            keepAll              : true,
                            reportDir            : 'target',
                            reportFiles          : 'apigeelint.html',
                            reportName           : 'Linting HTML Report'
                    ])
                }
            }
        }
        stage('Pre-Deployment Configurations for API ') {
                steps {
             dir('edge') {
               println "Predeployment Configurations for targetservers "
                          sh "mvn -Pdev-1 -Denv=${params.apigee_env} -Dorg=${params.apigee_org} " +
                              "    -Dapigee.config.options=create -Dapigee.config.dir=resources/edge " +
                              "    apigee-config:targetservers"
                      }
                    }
                }
            stage('Build proxy bundle') {
                steps {
                    dir('edge') {
                        sh "mvn package -Pdev-1 -Denv=${params.apigee_env} -Dorg=${params.apigee_org}"

                    }
                }
            }
            stage('Deploy proxy bundle') {
                steps {
                    dir('edge') {
                            sh "mvn install -Pdev-1 -Denv=${params.apigee_env} -Dorg=${params.apigee_org}"
                    }
                }
            }
                 stage('Post-Deployment Configurations for API ') {
                steps {
             dir('edge') {
               println "Post-Deployment Configurations for API Products Configurations, App Developer and App Configuration "
                          sh "mvn -Pdev-1 -Denv=${params.apigee_env} -Dorg=${params.apigee_org} " +
                              "    -Dapigee.config.options=create -Dapigee.config.dir=resources/edge " +
                              "    apigee-config:apiproducts " +
                              "    apigee-config:developers apigee-config:developerapps"
                      }
                    }
                }    
            /*stage('Functional Test') {
                steps {
                dir('edge') {
                 sh "node ./node_modules/cucumber/bin/cucumber-js target/test/integration/features --format json:target/reports.json" 
            }
            }
        }
            stage('Functional Test Report') {
                steps {

                dir('edge') {
                    step([
                             $class             : 'CucumberReportPublisher',
                            fileExcludePattern : '',
                            fileIncludePattern : "reports.json",
                            ignoreFailedTests  : false,
                            jenkinsBasePath    : '',
                            jsonReportDirectory: "target",
                            missingFails       : false,
                            parallelTesting    : false,
                            pendingFails       : false,
                            skippedFails       : false,
                            undefinedFails     : false       
                    ])
                }
            }
        }*/

        }
    }
