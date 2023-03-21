pipeline {
    agent any

    environment {
    DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
    PATH=sh(script:"echo $PATH:/usr/local/bin", returnStdout:true).trim()
    ANS_KEYPAIR="firstkey.pem"
    AWS_REGION="us-east-1"

    }
    stages {

            stage('Build Artifact') {
                        steps {
                            echo 'Building Artifact'
                            sh 'chmod +x ./todoserver/mvnw'
                            sh 'cd todoserver && ./mvnw clean package -DskipTests'                            
                            sh "cd todoserver && cp target/todo-server-0.0.1.jar release"

                        }
                    }
            stage('Build App Docker Images') {
                        steps {
                            echo 'Building App Dev Images'
                            sh 'echo $DOCKER_PASSWORD | docker login -u karacaaslan --password-stdin'
                            sh "docker build -t karacaaslan/todoserver -f ./todoserver/Dockerfile ./todoserver"
                            sh 'docker push karacaaslan/todoserver'
                            sh "docker build -t karacaaslan/todoapp -f ./todoapp/Dockerfile ./todoapp"
                            sh 'docker push karacaaslan/todoapp'
                        }
                    }

            stage('Deploy App on Kubernetes cluster'){
                        ansiblePlaybook(
                            inventory: "",
                            installation: "ansible",
                            limit: "",
                            playbook: "/todo/k8sdeployplaybook.yml",
                            extras: ""
                        )
                            
            }
        }     
            
    }






