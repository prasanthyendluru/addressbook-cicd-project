pipeline{
    agent any
    tools {
        maven 'maven'
    }
    stages{
        stage('Checkout'){
          steps{
                 git url: 'https://github.com/prasanthyendluru/addressbook-cicd-project.git' , branch: 'main'
          }
        }
        stage('Build with Maven'){
          steps{
                 sh 'mvn clean package -DskipTests'
          }
        }
        stage('Install Tomcat'){
            steps{
                withCredentials([sshUserPrivateKey(credentialsId:'prasanth-id',
                                                   keyFIleVariable: 'SSH_KEY',
                                                   usernameVariable: 'SSH_USER' )])
                
                {
                    sh ''' 
                    ANSIBLE_HOST_KEY_CHECKING=FALSE \
                    ansible-playbook -i "3.9.18.202",-u
                    $SSH_USER --private-key $SSH_KEY ansible/tomcat-setup.yml
                    '''
                }
            }
        }
       stage('DEPLOY WAR'){
            steps{
                withCredentials([sshUserPrivateKey(credentialsId:'prasanth-id',
                                                   keyFIleVariable: 'SSH_KEY',
                                                   usernameVariable: 'SSH_USER' )])
                
                {
                    sh ''' 
                    ANSIBLE_HOST_KEY_CHECKING=FALSE \
                    ansible-playbook -i "3.9.18.202",-u
                    $SSH_USER --private-key $SSH_KEY \
                    ansible/deploy.yml --extra-vars
                    "war_file=${WORKSPACE}/target/addressbook.war app_name=addressbook"
                    '''
                }
            }
        }
        
       
    }
}
