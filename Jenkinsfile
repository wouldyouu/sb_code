pipeline {
    agent any
    
    tools {
        maven 'my_maven'
    }
    environment {
        GITNAME = 'wouldyou'            
        GITEMAIL = 'jhj5781@gmail.com' 
        GITWEBADD = 'https://github.com/wouldyou/sb_code.git'
        GITSSHADD = 'git@github.com:wouldyou/spring_deployment.git'
        GITCREDENTIAL = 'git_cre'
        
        DOCKERHUB = 'wouldyou/spring'
        DOCKERHUBCREDENTIAL = 'docker_cre'
    }
        
    stages {
        stage('Checkout Github') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [],
                userRemoteConfigs: [[credentialsId: GITCREDENTIAL, url: GITWEBADD]]])
            }
        
        
            post {
        
                failure {
                    echo 'Repository clone failure'
                }
                success {
                    echo 'Repository clone success'
                }
            }
        }
        
        
        stage('code build') {
            steps {
                sh "mvn clean package"
                
            }
        }
        stage('image build') {
            steps {
                sh "docker build -t ${DOCKERHUB}:${currentBuild.number} ."
                sh "docker build -t ${DOCKERHUB}:latest ."
                // currentBuild.number = 젠킨스가 제공하는 빌드넘버 변수
                // oolralra/spring:1 같은 형태로 빌드가 될것
            }
        }
        
        stage('image push') {
            steps {
                withDockerRegistry(credentialsId: DOCKERHUBCREDENTIAL, url: '') {
                    sh "docker push ${DOCKERHUB}:${currentBuild.number}"
                    sh "docker push ${DOCKERHUB}:latest"
                // withDockerRegistry : docker pipeline 플러그인 설치시 사용가능
                // DOCKERHUBCREDENTIAL= docker_cre 만들어둔 credentials
                }
            }
            
            post {
                failure {
                    echo 'docker image push failure'
                    sh "docker image rm -f ${DOCKERHUB}:${currentBuild.number}"
                    sh "docker image rm -f ${DOCKERHUB}:latest"
                }
                
                success {
                    echo 'docker image push success'
                    sh "docker image rm -f ${DOCKERHUB}:${currentBuild.number}"
                    sh "docker image rm -f ${DOCKERHUB}:latest"
                }
            }
        }
        stage('k8s manifest file update') {
            steps {
                git credentialsId: GITCREDENTIAL,
                url: GITSSHADD,
                branch: 'main'
        
                // 이미지 태그 변경 후 메인 브랜치에 푸시
                sh "git config --global user.email ${GITEMAIL}"
                sh "git config --global user.name ${GITNAME}"
                sh "sed -i 's@${DOCKERHUB}:.*@${DOCKERHUB}:${currentBuild.number}@g' deployment.yml"
        
                sh "git add ."
                sh "git commit -m 'fix:${DOCKERHUB} ${currentBuild.number} image versioning'"
                sh "git branch -M main"
                sh "git remote remove origin"
                sh "git remote add origin ${GITSSHADD}"
                sh "git push -u origin main"

            }
            post {
                failure {
                echo 'k8s manifest file update failure'
                }
                success {
                echo 'k8s manifest file update success'  
                }
            }
        }

    }
}
