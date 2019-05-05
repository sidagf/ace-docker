pipeline {
    agent none
    environment {
        ACE_INSTALL     =   '10.0.0.12-IIB-LINUX64-DEVELOPER.tar.gz'
        ACE_LOCATION    =   '/home/simen/'
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
        }
        stage('Run ace-docker') {
            agent any
            steps {
                sh 'docker run --name aceserver -p 7600:7600 -p 7800:7800 -p 7843:7843 --env LICENSE=accept --env ACE_SERVER_NAME=ACESERVER ace-only:latest'
            }
        }
        stage('Test ace-docker') {
            agent any
            steps {
                sh 'curl localhost:7600'
            }
        }
    }
}
