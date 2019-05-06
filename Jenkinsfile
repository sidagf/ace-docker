pipeline {
    agent none
    environment {
        ACE_INSTALL     =   '11.0.0.4-ACE-LINUX64-DEVELOP.tar.gz'
        ACE_LOCATION    =   '/home/jenkins/downloads/'
        INTERFACE       = 'eth0'
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
                retry(5)
            }
        }
        stage('Run ace-docker') {
            agent any
            steps {
                sh 'docker run -d --name ace-docker-demo -p 7600:7600 -p 7800:7800 -p 7843:7843 --env LICENSE=accept --env ACE_SERVER_NAME=ACESERVER ace-dev-only:latest'
            }
        }
        stage('Test ace-docker') {
            agent any
            steps {
                sleep(30)
                sh 'curl $(ip -f inet addr show eth0 | grep -Po 'inet \K[\d.]+'):7600'
            }
            options {
                retry(10)
            }
        }
    }
}
