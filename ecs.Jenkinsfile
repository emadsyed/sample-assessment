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
      stage('Deploy Application') {
       """#!/bin/bash
       TASK_FAMILY="test-service"
       # Create a new task definition for this build
       sed -e "s;%BUILD_NUMBER%;${BUILD_NUMBER};g" task-definition.json > task-deifnition-v_${BUILD_NUMBER}.json
       aws ecs register-task-definition --family test-services --cli-input-json file://task-definition-v_${BUILD_NUMBER}.json # Update the service with the new task definition and desired count
       TASK_REVISION=`aws ecs describe-task-definition --task-definition task-definition | egrep "revision" | tr "/" " " | awk '{print $2}' | sed 's/"$//'`
       DESIRED_COUNT=`aws ecs describe-services --services ${serviceName} | egrep "desiredCount" | tr "/" " " | awk '{print $2}' | sed 's/,$//'`
        if [ ${DESIRED_COUNT} = "0" ]; then
          DESIRED_COUNT="1"
        fi
       aws ecs update-service --cluster default --service ${serviceName} --task-definition ${TASK_FAMILY}:${TASK_REVISION} --desired-count ${DESIRED_COUNT}
      """
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
  
}