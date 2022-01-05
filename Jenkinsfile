pipeline {
    agent any 
    stages {
        stage ('Build') {
            steps{
                echo 'Building..'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage ('Stage') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword( credentialId: 'webserver_key', usernameVariable: '$USERNAME', passwordVariable: '$PASSWD')]) {
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: '$USERNAME',
                                    encryptedPassphrase: '$PASSWD'
                                ],
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'dist/trainSchedule.zip',
                                        removePrefix: 'dist/',
                                        remoteDirectory: '/tmp',
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/train-schedule.zip -d /opt/train-schedule/ && sudo /usr/bin/systemctl start train-schedule'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}
