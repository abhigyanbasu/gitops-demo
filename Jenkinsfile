pipeline {
    agent any
    environment {
        RELEASE = "1.0.0"
        DOCKERHUB_USERNAME = "abhigyanbasu"
        APP_NAME = "gitops-demo-app"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
        }
    stages {
        stage('Cleanup Workspace'){
            steps {
                script {
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps {
                git credentialsId: 'github', 
                url: 'https://github.com/abhigyanbasu/gitops-demo.git',
                branch: 'master'
            }
        }
        stage('Build Docker Image'){
            steps {
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){
            steps {
                script{
                    docker.withRegistry('', REGISTRY_CREDS ){
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        } 
        stage('Delete Docker Images'){
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
              //  sh "docker rmi ${IMAGE_NAME}:latest"
            }
        }
        stage('Trigger config change pipeline'){
            steps {
                //sh "curl -v -k --user admin:11cd38cb233da32a8c630c63a2ca9df42e -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'http://52.38.89.154:8080/job/gitops-deploy/buildWithParameters?token=gitops-config1'"
               sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'https://jenkins.abhgyan.pics/job/gitops-deployment-pipeline/buildWithParameters?token=gitops-token'"
                
               // build job: 'gitops-deploy', parameters: [string(name: 'IMAGE_NAME', value:"${IMAGE_NAME}")]
            }
        }
     //   stage('Push the changed deployment file to Git'){
       //     steps {
         //       script{
           //         sh """
             //       git config --global user.name "vikram"
               //     git config --global user.email "vikram@gmail.com"
                 //   git add deployment.yml
                   // git commit -m 'Updated the deployment file' """
                    //ithCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    //    sh "git push http://$user:$pass@github.com/kunchalavikram1427/gitops-demo.git dev"
                   // }
               // }
            //}
        }
    }



// stage('Build Docker Image'){
//             steps {
//                 sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
//                 sh "docker build -t ${IMAGE_NAME}:latest ."
//             }
//         }
//         stage('Push Docker Image'){
//             steps {
//                 withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'pass', usernameVariable: 'user')]) {
//                     sh "docker login -u $user --password $pass"
//                     sh "docker push ${IMAGE_NAME}:${IMAGE_TAG} ."
//                     sh "docker push ${IMAGE_NAME}:latest ."
//                 }
//             }
//         }

