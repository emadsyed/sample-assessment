 //Define all variables
  def project = 'Assessment'
  def appName = 'basic-nodejs'
  def serviceName = "${appName}-sample"  
  def imageVersion = 'test'
  def namespace = 'development'
  def imageTag = "esyed/${project}/${appName}:${imageVersion}.${env.BUILD_NUMBER}"
  
node('node') {

    try {

       stage('Checkout'){  //Checking out Code from repository

          checkout scm
       }

       stage('Build-image') {  //Building Docker Image
          sh("docker build -t ${imageTag} .")
       }

       stage('Push-to-registry') {   //Pushing Image to Docker regisery
           sh("docker -- push ${imageTag}")
        }

       stage('Deploy'){  //Deploying Image

         echo 'Push to Repo'
         sh './dockerPushToRepo.sh'

         echo 'ssh to web server and tell it to pull new image'
         sh 'ssh deploy@xxxxx.xxxxx.com running/xxxxxxx/dockerRun.sh'

       }

       stage('Cleanup'){

         echo 'prune and cleanup'
         sh 'npm prune'
         sh 'rm node_modules -rf'

         mail body: 'project build successful',
                     from: 'xxxx@yyyyy.com',
                     replyTo: 'xxxx@yyyy.com',
                     subject: 'project build successful',
                     to: 'yyyyy@yyyy.com'
       }



    }
    catch (err) {

        currentBuild.result = "FAILURE"

            mail body: "project build error is here: ${env.BUILD_URL}" ,
            from: 'xxxx@yyyy.com',
            replyTo: 'yyyy@yyyy.com',
            subject: 'project build failed',
            to: 'zzzz@yyyyy.com'

        throw err
    }

}