pipeline {

   environment {         // ensure app object available across stages
   def webapp = '' 
   def dbapp = ''
   def webcont = ''
   def dbcont= ''     
   }                     // close environment

   agent any             //execute on any agent/node
   tools
       {
       maven '3.5.2'
       jdk 'don'
       }
  stages {
   
 
// ############# get code ##############   
   
      stage('Pulling Code') {   // check out code from gitlab
         steps {
            script {
            
//              docker.withServer('tcp://docker.donemmerson.co.uk:2376','becb15d9-c188-4bf1-b0ed-27b34849688f') {
               
              git branch: 'master', credentialsId: '585d90d4-4d66-42ff-ad2f-7a4b7615f623', url: 'https://gitlab.com/Don-Emmerson/registration-docker.git'
//      }
           }             // close script 
        }            // close steps
     }            //close stage

// ############# Build db ##############

       stage ('Build db App') {            // build image
 	     steps {
 	        script{                // build app
               dbapp = docker.build ("usrdb", "registration-database/")
       
            }             //close script   
         }             //close steps
       }             //close stage
       
// ############# Build web app ##############       
           
 	  stage ('Build Web App') {            // build image
 	     steps {
 	        script{                // build app
 
      // Run the maven build

               sh "mvn -f app/pom.xml clean test package -U"         
           
       
            }             //close script   
         }             //close steps
      }             //close stage
      
//################ Add app to docker ###############
     	
     stage ('Build Docker Web App') {            // build image
 	    steps {
 	       script{                // build app

               sh "cp /home/ubuntu/workspace/labpip/app/target/UserSignup.war /home/ubuntu/workspace/labpip/registration-webserver/"
               webapp = docker.build ("usrsignup", "registration-webserver/")
           
            }             //close script   
         }             //close steps
      }             //close stage
             
      
// ############# Commit dev version ##############
      
     stage ('Commit dev') {
        steps {
           script {
       
               docker.withRegistry('https://docker.donemmerson.co.uk:443', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
               webapp.push('dev')
               dbapp.push('dev')
       
              }              // close docker.with.reg
           }              // close script
        }              //close step
     }              //close stage 
     
// ############# Deploy to dev  ##############
      
     stage ('Deploy Docker Image') {
        steps {
           script{
//                
              sh "sudo cp /hosts/inventory /etc/ansible/hosts" 
              ansiblePlaybook(credentialsId: '8838ded7-6c9a-48c9-9963-997d5c8a9b7f', inventory: 'dev', playbook: 'dev_deploy.yml')
   
  
              ansibleTower (credential: 'slave1', 
                            extraVars: '', 
                            importTowerLogs: true,
                            importWorkflowChildLogs: false,
                            inventory: 'Dev_Docker_Inventory',
                            jobTags: '',
                            jobTemplate: 'usersignup_install',
                            limit: '',
                            removeColor: false,
                            templateType: 'job',
                            towerServer: 'Ansible tower',
                            verbose: true)
           }             //close script   
        }             //close steps
     }             //close stage
     
// ############# Test Dev Deploy ##############       
           
 	  stage ('Dev Test') {            // test dev instance
 	     steps {
 	        script{                
 
      // Run the maven test

               sh "mvn -f app/pom.xml test"         
           
           }             //close script   
        }             //close steps
     }             //close stage
      

// ############# Commit ##############
      
      stage ('Commit to Prod') {
         steps {
            script {
       
               docker.withRegistry('https://docker.donemmerson.co.uk:443/', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
               webapp.push('latest')
               dbapp.push('latest')

                }              // close docker.with.registry
            }              // close script
         }              //close steps
      }              //close stage   

      stage ('deploy to Prod') {
         steps{
            script {
                ansibleTower (credential: 'slave1', 
                              extraVars: '', 
                              importTowerLogs: true,
                              importWorkflowChildLogs: false,
                              inventory: 'Prod_Docker_Inventory',
                              jobTags: '',
                              jobTemplate: 'usersignup_install',
                              limit: '',
                              removeColor: false,
                              templateType: 'job',
                              towerServer: 'Ansible tower',
                              verbose: true)
            }                // end scripts

         }                // end steps

       }                 // end stage

   }              // close stages 
}             // close pipeline    