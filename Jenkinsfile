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
   
      stage('Get code') {   // check out code from gitlab
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
           
 	  stage ('Build Web App') {            // build image
 	     steps {
 	        script{                // build app
 
      // Run the maven build

               sh "mvn -f app/pom.xml clean test package -U"         
//             sh "cp /var/lib/jenkins/workspace/labpip/app/target/UserSignup.war /var/lib/jenkins/workspace/labpip/registration-webserver/"
               webapp = docker.build ("usrsignup", "registration-webserver/")
           
//       } 

       
            }             //close script   
         }             //close steps
      }             //close stage
      
           
      
// ############# Commit dev version ##############
      
//      stage ('Commit dev to registry') {
//         steps {
//            script {
//       
//               docker.withRegistry('https://docker.donemmerson.co.uk:5000', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
//               app.push('dev')
//       
//               }              // close docker.with.reg
//            }              // close script
//         }              //close step
//      }              //close stage 
     
// ############# Deploy code ##############
      
//      stage ('Deploy Docker Image') {
//        steps {
//            script{
//              docker.withRegistry('https://docker.donemmerson.co.uk:5000', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
//              docker.withServer('tcp://docker.donemmerson.co.uk:2376', 'dockerSSL') {
//                docker.pull('https://docker.donemmerson.co.uk:5000/bamboo:dev') {
//                }
//               docker.withServer('tcp://docker.donemmerson.co.uk:2376', 'dockerSSL') {
//               container = app.run("--name bamboo -d -p 3000:3000") 
//               
//              def cont = image.run("bamboo:dev") 
//}
//            }                //close dockerwithreg.
//               }              //close dockerwithserver
//            }             //close script   
//         }             //close steps
//      }             //close stage
     
// ############# Commit ##############
      
      stage ('Commit to registry') {
         steps {
            script {
//             docker.withServer('tcp://docker.donemmerson.co.uk:2376','becb15d9-c188-4bf1-b0ed-27b34849688f') {
       
               docker.withRegistry('https://docker.donemmerson.co.uk:443/', '4aa2c853-54a6-40b9-8fca-0fc13d9a26a9') {
               webapp.push()
               dbapp.push()
 

//}      
               }              // close docker.with.registry
            }              // close script
         }              //close steps
      }              //close stage   
      stage ('deploy with ansible') {
         steps{
            script {
                ansibleTower (credential: 'slave1', 
                              extraVars: '', 
                              importTowerLogs: true,
                              importWorkflowChildLogs: false,
                              inventory: 'Test_Docker_Inventory',
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