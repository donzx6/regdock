pipeline {

   environment {         // ensure app object available across stages
   def webapp = '' 
   def dbapp = ''
   def webcont = ''
   def dbcont= ''     
   }                     // close environment

   agent any
           //execute on any agent/node
   tools
       {
       maven '3.5.2'
       jdk 'don'
       }
  stages {
   
 
// ############# get code ##############   
   
      stage('Pulling Code') {   // check out code from gitlab
//      agent {
//          label 'slave1'
//      }

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
// 	   agent {
//          label 'slave1'
//      }
 	     steps {
 	        script{                // build app
               dbapp = docker.build ("usrdb", "registration-database/")
       
            }             //close script   
         }             //close steps
       }             //close stage
       
// ############# Build web app ##############       
           
 	  stage ('Build Web App') {            // build image
// 	   agent {
//          label 'slave1'
//      }
 	     steps {
 	        script{                // build app
 
      // Run the maven build

               sh "mvn -f app/pom.xml clean package -U "         
           
       
            }             //close script   
         }             //close steps
      }             //close stage
      
//################ Add app to docker ###############
     	
     stage ('Build Docker Web App') {            // build image
// 	   agent {
//          label 'slave1'
//      }
 	    steps {
 	       script{                // build app

               sh "echo $pwd"
               sh "cp /home/ubuntu/workspace/labpip/app/target/UserSignup.war /home/ubuntu/workspace/labpip/registration-webserver/"
               webapp = docker.build ("usrsignup", "registration-webserver/")
           
            }             //close script   
         }             //close steps
      }             //close stage
             
      
// ############# Commit dev version ##############
      
     stage ('Commit dev') {
//       agent {
//          label 'slave1'
//      }
        steps {
           script {
       
               docker.withRegistry('https://docker.donemmerson.co.uk:443', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
               webapp.push('latest')
               dbapp.push('latest')
       
              }              // close docker.with.reg
           }              // close script
        }              //close step
     }              //close stage 
     
// ############# Deploy to dev  ##############
      
     stage ('Deploy Docker Image') {
//       agent {
//          label 'ansible_master'
//      }
        steps {
           script{
                
             ansiblePlaybook(credentialsId: '8838ded7-6c9a-48c9-9963-997d5c8a9b7f', inventory: 'inventory/hosts-dev', playbook: 'imgpull.yml')
   
           }             //close script   
        }             //close steps
     }             //close stage
     
// ############# Test Dev Deploy ##############       
           
 	  stage ('Dev Test') {            // test dev instance
//        agent {
//          label 'slave1'
//        }
 
 	     steps {
 	        script{                
 
      // Pull the test app
               git branch: 'master', credentialsId: '585d90d4-4d66-42ff-ad2f-7a4b7615f623', url: 'https://gitlab.com/Don-Emmerson/Testapp.git'

               sh "mvn -f Testapp/pom.xml test"         
           
           }             //close script   
        }             //close steps
     }             //close stage
      

// ############# Commit ##############
      
      stage ('Commit to Prod') {
//        agent {
//          label 'slave1'
//      }
        
         steps {
            script {
       
               docker.withRegistry('https://docker.donemmerson.co.uk:443/', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
//               webapp.push('latest')
//               dbapp.push('latest')

                }              // close docker.with.registry
            }              // close script
         }              //close steps
      }              //close stage   

//      stage ('deploy to Prod') {
//       agent {
//          label 'ansible_master'
//      }
       
       
//         steps {
//            script {
//                ansibleTower (credential: 'slave1', 
//                              extraVars: '', 
//                              importTowerLogs: true,
//                              importWorkflowChildLogs: false,
//                              inventory: 'Prod_Docker_Inventory',
//                              jobTags: '',
//                              jobTemplate: 'usersignup_install',
//                              limit: '',
//                              removeColor: false,
//                              templateType: 'job',
//                              towerServer: 'Ansible tower',
//                              verbose: true)
//            }                // end scripts

//         }                // end steps

//       }                 // end stage

   }              // close stages 
}             // close pipeline    