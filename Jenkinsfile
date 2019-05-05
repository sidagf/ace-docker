pipeline {
    agent none
    environment {
        ACE_INSTALL     =   '11.0.0.4-ACE-LINUX64-DEVELOP.tar.gz'
        ACE_LOCATION    =   '/home/jenkins/downloads/'
    }
    stages {
        stage('Check that ACE is available') {
            agent any
            steps {
                sh 'ls -la $ACE_LOCATION$ACE_INSTALL'
            }
            
        }
        stage('Copy ACE to deps folder') {
            agent any
            steps {
                sh 'cp $ACE_LOCATION$ACE_INSTALL $WORKSPACE$JOBNAME/deps/'
                sh 'ls -la $WORKSPACE$JOBNAME/deps/$ACE_INSTALL'
            }
        }
        stage('Build ace-docker') {
            agent any
            steps {
                sh 'docker build -t ace-dev-only --build-arg ACE_INSTALL=$ACE_INSTALL --file ubuntu/Dockerfile.aceonly .'
            }
            options {
                retry(3)
            }
        }
        stage('Build ace-sample') {
            agent any
            steps {
                sh 'docker build -t aceapp --file sample/Dockerfile.aceonly .'
            }
        }
        stage('Run ace-sample') {
            agent any
            steps {
                sh 'docker run --name aceapp -p 7600:7600 -p 7800:7800 -p 7843:7843 --env LICENSE=accept --env ACE_SERVER_NAME=ACESERVER --mount type=bind,src=$WORKSPACE$JOBNAME/sample/initial-config,dst=/home/aceuser/initial-config --env ACE_TRUSTSTORE_PASSWORD=truststorepwd --env ACE_KEYSTORE_PASSWORD=keystorepwd aceapp:latest'
            }
            post {
                failure {
                    sh 'docker container rm aceapp --force'
                }
            }
        }
    }
}
