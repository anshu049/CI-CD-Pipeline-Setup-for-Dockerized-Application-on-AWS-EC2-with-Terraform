pipeline {
    agent any
    
    environment {
        IMAGE_NAME = 'anshu049/tweet-app'
        DOCKER_CREDS_USR = credentials('docker-hub-username')
        DOCKER_CREDS_PSW = credentials('docker-hub-password')
        AWS_ACCESS_KEY_ID = credentials('access_key')
        AWS_SECRET_ACCESS_KEY = credentials('secret_access_key')
        SSH_KEY_CREDENTIAL = 'server-ssh-key'
    }

        }
        stage('build image') {
            steps {
                echo 'building docker image...'
                sh "docker build -t ${IMAGE_NAME} ."
                dockerLogin()
                dockerPush(env.IMAGE_NAME)
            }
        }
        stage('provision server') {
            environment {
                TF_VAR_env_prefix = 'test'
                AWS_ACCESS_KEY_ID = credentials('access_key')
                AWS_SECRET_ACCESS_KEY = credentials('secret_access_key')
            }
            steps {
                script {
                    dir('terraform') {
                        sh "terraform init"
                        sh "terraform apply --auto-approve"
                        EC2_PUBLIC_IP = sh(
                            script: "terraform output ec2_public_ip",
                            returnStdout: true
                        ).trim()
                    }
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    echo "waiting for EC2 server to initialize" 
                    sleep(time: 90, unit: "SECONDS") 

                    echo 'deploying docker image to EC2...'
                    echo "${EC2_PUBLIC_IP}"

                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME} ${env.DOCKER_CREDS_USR} ${env.DOCKER_CREDS_PSW}"
                    def ec2Instance = "ec2-user@${EC2_PUBLIC_IP}"

                    sshagent(['server-ssh-key']) {
                        sh "scp -o StrictHostKeyChecking=no server-cmds.sh ${ec2Instance}:/home/ec2-user"
                        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "ssh ${ec2Instance} ${shellCmd}"
                    }
                }
            }
        }
    }
}

def dockerLogin() {
    sh "echo ${DOCKER_CREDS_PSW} | docker login -u ${DOCKER_CREDS_USR} --password-stdin"
}

def dockerPush(imageName) {
    sh "docker push ${imageName}"
}