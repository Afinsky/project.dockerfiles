pipeline {
    agent { label 'node_01' }

    environment {
      registry = "afinsky/ansible"
  }
   
   
    stages {
        
        stage('Clear workspace') {
           steps {
              deleteDir()
            }
        }
        
        stage('Clone repository') { 
            steps { 
                git branch: "master", url: "git@github.com:Afinsky/project.dockerfiles.git"
            }
        }

        stage('Building image') {
            steps {
              script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
        }


        stage('Run container') {
          steps {
              sh """
                 docker run --rm -v \$(pwd):/ansible/playbooks \$registry:\$BUILD_NUMBER --version
                 """
            }
        }


        stage('Run ansible') {
          steps {
              sh "ls -l"

              sh """
                 docker run --rm \
                    -v ~/.ssh/id_rsa:/root/.ssh/id_rsa \
                    -v ~/.ssh/id_rsa.pub:/root/.ssh/id_rsa.pub \
                    -v \$(pwd):/ansible/playbooks \
                    \$registry:\$BUILD_NUMBER -i inv.yaml xwiki.yaml
                 """
            }
        }

        stage('Remove Unused docker image') {
          steps{
            sh "docker rmi $registry:$BUILD_NUMBER"
          }
        }

    }
}
