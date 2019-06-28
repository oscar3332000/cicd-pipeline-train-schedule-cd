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
        
        stage('Deploy to staging') {
            when { 
                branch 'master'
            }
            steps {
                echo 'Deploying to staging'
                
                withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'USERPASS', usernameVariable: 'USERNAME')]) {
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging', 
                                sshCredentials: [ encryptedPassphrase: '$USERPASS', username: '$USERNAME'], 
                                transfers: [
                                    sshTransfer(
                                        excludes: '', 
                                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule', 
                                        execTimeout: 120000, 
                                        flatten: false, 
                                        makeEmptyDirs: false, 
                                        noDefaultExcludes: false, 
                                        patternSeparator: '[, ]+', 
                                        remoteDirectory: '/tmp/', 
                                        remoteDirectorySDF: false, 
                                        removePrefix: 'dist/', 
                                        sourceFiles: 'dist/trainSchedule.zip'
                                        )
                                    ], 
                                    usePromotionTimestamp: false, 
                                    useWorkspaceInPromotion: false, 
                                    verbose: false
                                    )
                                ]
                            )
                   }

                
                
            } //steps
            
        }
    }
}
