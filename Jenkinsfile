//Descriptuon: "pipeline to deply golang backend application to kubernetes"
pipeline {
  options{
    skipDefaultCheckout()
    buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '2',daysToKeepStr: '1'))
  }
  environment{
      image_name = 'vin1711/speed-poc'
    }
  agent {
    kubernetes {
      cloud 'kubernetes'
      label 'speed-poc'  // all your pods will be named with this prefix, followed by a unique id
      idleMinutes 5  // how long the pod will live after no jobs have run on it
      yamlFile 'pod.yaml'  // path to the pod definition relative to the root of our project 
      defaultContainer 'git'  // define a default container 
      podRetention never()
    }
  }
  stages {
    stage('checkout'){
      steps{
            git branch:'main',url:'https://github.com/VinYuvian/fiberReact_back.git'
            //sh 'git config --global --unset http.proxy'
            //sh 'git clone https://github.com/VinYuvian/fiberReact_back.git'
            //stash 'workspace'
      }
    }
    stage('Build Docker Image') {
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
    //stage('deploy to kubernetes'){
      //steps{ 
         //unstash 'workspace'
           //kubernetesDeploy(configs: '**/*.yaml', kubeconfigId:'kubeConfig',secretNamespace:'jenkins',enableConfigSubstitution:true,deleteResource:true)
        //   kubernetesDeploy(configs: '**/*.yaml', kubeconfigId:'kubeConfig',secretNamespace:'jenkins',enableConfigSubstitution:true)
      //}
    //}
   }
 }
