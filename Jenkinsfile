def dockerImage = ''
def projects = ["node-api","go-api"] 

pipeline {
    environment {
        artifactRegistryAuthURL = "https://asia-southeast2-docker.pkg.dev"
        registry_url = 'asia-southeast2-docker.pkg.dev/rect-devops-training/rectgistry/'
        app_name = 'sample-backend'
        KUBECONFIG = credentials('kubeconfig-file')
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gke-service-account')
    }

    agent any

    stages {
        stage ('Build Nx Monorepo') {
            agent {
                docker {
                    image 'harispraba/nx-builder:alpine'
                    args  '-v /tmp:/tmp'
                }
            }
            steps {
                script{
                    data.each {val->  
                        sh "nx run $val:build"
                    }
                }
            }
        }
        stage('Test Nx Monorepo') {
            agent {
                docker {
                    image 'harispraba/nx-builder:alpine'
                    args  '-v /tmp:/tmp'
                }
            }
            steps {
                script{
                    data.each {val->  
                        sh "nx run $val:test --passWithNoTests"
                    }
                }
            }
        }
        stage('Build and push docker image') {
            steps {
                script{
                    data.each {val->
                        tag = registry_url+val
                        sh "docker build -f apps/$val/Dockerfile . -t $tag"
                    }
                }
            }
        }
        stage('Push to Artifact Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'artifactRegistryAuth', passwordVariable: 'artifactRegistryKey']) {
                    sh "docker login $artifactRegistryAuthURL -u _json_key -p ${env.artifactRegistryKey}"
                    sh "docker push shanem/spring-petclinic:latest"
                    script{
                        data.each {val->
                            tag = registry_url+val
                            sh "docker push $tag"
                        }
                    }
                }
            }
        }
        stage ('Deploy Kubernetes') {
            agent {
                docker {
                    image 'harispraba/kubectl'
                    args '--entrypoint=""'
                }
            }
            steps {
                script{
                    data.each {val->
                        sh "kubectl apply -f apps/$val/deployment/manifest.yaml"
                    }
                }
            }
        }
    }
}
