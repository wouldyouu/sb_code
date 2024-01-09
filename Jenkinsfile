pipeline {
    agent any
    
    tools {
        maven 'my_maven'
    }
    environment {
        GITNAME = 'wouldyou'
        GITEMAIL = 'jhj5781@gmail.com'
        GITWEBADD = 'https://github.com/wouldyouu/sb_code.git'
        GITSSHADD = 'git@github.com:wouldyouu/sb_code.git'
        GITCREDENTIAL = 'git_cre'
        }
    stages {
        stage('Build') {
            steps {
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
