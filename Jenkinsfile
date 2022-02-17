

pipeline {
    agent any
    stages 
    {      
        stage('Init')
        {
            agent{
                label 'node'
            }
             steps {
                script {
                    lastCommitInfo = sh(script: "git log -1", returnStdout: true).trim()
                    commitContainsSkip = sh(script: "git log -1 | grep 'skip ci'", returnStatus: true)
                    slackMessage = "*:${env.JOB_NAME}* *${env.BRANCH_NAME}* received a new commit. \nHere is commmit info: ${lastCommitInfo}\n*Console Output*: <${BUILD_URL}/console | (Open)> Username : mb_dev   Password:mindbowser@123@ \n :hourglass_flowing_sand::hourglass_flowing_sand:"
                    //send slack notification of new commit
                    slack_send(slackMessage)
                    //if commit message contains skip ci
                    if(commitContainsSkip == 0) {
                        skippingText = " Skipping Build for *${env.BRANCH_NAME}* branch."
                        env.shouldBuild = false
                        currentBuild.result = 'ABORTED'
                        slack_send(skippingText,"warning")
                        error('BUILD SKIPPED')
                        
                    }
                }
            }//step end
        }

        stage('preparation')
                {
                    steps{
                        script
                            {
                                branch_name = "${env.BRANCH_NAME}"
                            
                                if(branch_name == "master")
                                {
                                    env.BUILD_TYPE = 'release'
                                    env.FASTLANE_ENV_FILE='prod'

                                }else if(branch_name == "devops_ashwin")
                                {
                                    env.BUILD_TYPE = 'debug'
                                    env.FASTLANE_ENV_FILE='dev' 

                                }else if(branch_name == "staging")
                                {
                                    env.BUILD_TYPE = 'staging'
                                    env.FASTLANE_ENV_FILE='staging'
                                }

                            }//script
                    }//steps
                }//preparation
        stage('Read YAML file') 
                {
                    steps {
                        script
                            { 
                                datas = readYaml (file: 'sample.yaml') 
                            }
                                echo datas.ear_file.deploy.toString()
                    }

                }
       

            }
} //pipeline
