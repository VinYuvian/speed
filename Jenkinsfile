//Descriptuon: "pipeline to deply golang backend application to kubernetes"

pipeline {
  options{
    //skipDefaultCheckout()
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '2',daysToKeepStr: '1'))
  }
  environment{
      image_name = 'vin1711/speed-poc'
    }
  agent none 
  stages {
    stage('checkout'){
      agent{
          kubernetes {
            cloud 'kubernetes'
            label 'speed-poc'  // all your pods will be named with this prefix, followed by a unique id
            idleMinutes 5  // how long the pod will live after no jobs have run on it
            yamlFile 'pod.yaml'  // path to the pod definition relative to the root of our project 
            defaultContainer 'docker'  // define a default container 
            podRetention never()
          }
      }
      steps{
            git branch:'main',url:'https://github.com/VinYuvian/speed.git'
            //sh 'git config --global --unset http.proxy'
            //sh 'git clone https://github.com/VinYuvian/fiberReact_back.git'
            //stash 'workspace'
      }
    }
    stage('Build Docker Image') {
      agent{
          kubernetes {
            cloud 'kubernetes'
            label 'speed-poc'  // all your pods will be named with this prefix, followed by a unique id
            idleMinutes 5  // how long the pod will live after no jobs have run on it
            yamlFile 'pod.yaml'  // path to the pod definition relative to the root of our project 
            defaultContainer 'docker'  // define a default container 
            podRetention never()
          }
      }
      steps {
          container('docker') {  
            sh "docker build -t ${image_name} -t ${image_name}:${BUILD_ID} ."
            script{
                env.choice = input message:"please select how to proceed",parameters:[choice(name:'build_type',
                                                                                         choices:'test\nprod\nstage\ntestPipeline',
                                                                                         description:'Is it a pipeline check or a deployment step?')]
            }
         }
      }
    }
    stage('Push Image'){
      agent{
          kubernetes {
            cloud 'kubernetes'
            label 'speed-poc'  // all your pods will be named with this prefix, followed by a unique id
            idleMinutes 5  // how long the pod will live after no jobs have run on it
            yamlFile 'pod.yaml'  // path to the pod definition relative to the root of our project 
            defaultContainer 'docker'  // define a default container 
            podRetention never()
          }
      }
      when{
        expression{
          env.choice == 'prod'
        }
      }
      steps{
        container('docker'){ //need to provide container info as the docker commands need to be executed in docker container
            withCredentials([usernamePassword(credentialsId:'dockerCred',usernameVariable:'user',passwordVariable:'password')]){
                sh 'docker login -u $user -p $password'
                sh "docker push ${image_name}:${BUILD_ID}"
                sh "docker push ${image_name}:latest"
            }
        }
      }
    }
    stage('deploy to kubernetes'){
      agent any
      steps{ 
        sh 'echo hello'
        //sh 'kubectl --kubeconfig /home/ubuntu/config apply -f /var/lib/jenkins/workspace/deploy3/kube/deployment.yaml'
         //unstash 'workspace'
           //kubernetesDeploy(configs: '**/*.yaml', kubeconfigId:'kubeConfig',secretNamespace:'default',enableConfigSubstitution:true,deleteResource:true)
           //kubernetesDeploy(configs: '**/*.yaml', kubeconfigId:'kubeConfig',secretNamespace:'default',enableConfigSubstitution:true)
        //  sh 'kubectl --kubeconfig /home/ubuntu/config apply -f deployment.yaml'
      }
    }
   }
 }
