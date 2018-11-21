pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
    }
}
stage ('DeployToStaging') {
    when {
        branch 'master'
    }
    steps {
        withCredentials([usernamePassword(credentialsId: 'servers_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                        sshPublisher{
                            failOnError: true,
                            ContinueOnError: false,
                            publishers: [
                                sshPublisherDesc(
                                    configName: 'gradle'
                                    sshCredentials: [
                                        username: "$USERNAME",
                                        encryptedPassphrase: "$USERPASS",
                                        ],
                                    transfers: [
                                        sshTransfer9
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/service train-schedule stop && rm -rf /opt/train-schedule/* && unzip /tmp/trainschedule.zip -d /opt/train-schedule && sudo /usr/bin/service train-schedule start'
                                        }
                                        ]
                                    )
                                ]
                            )
                        }
        }
       
        
