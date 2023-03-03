pipeline
{
    agent any
        environment {
       AWS_ACCESS_KEY_ID=""
       AWS_SECRET_ACCESS_KEY=""
       TF_VAR_ami_linux="${ami_linux}"
       TF_VAR_ec2_instance_type1="${ec2_instance_type1}"
       TF_VAR_key_name="${key_name}"
       TF_VAR_ec2_instance_name="${ec2_instance_name}"
       TF_VAR_access_key="${access_key}"
       TF_VAR_secret_key="${secret_key}"
       TF_VAR_public_key="${public_key}"

    }
 parameters {
        string(name: 'ami_linux', defaultValue: 'ami-0557a15b87f6559cf', description: 'AMI setup',)
        string(name: 'ec2_instance_type1', defaultValue: 't2.micro', description: 'ec2 instance type',)
        string(name: 'key_name', defaultValue: 'ec2-instance-tf', description: 'ssh key name',)
        string(name: 'ec2_instance_name', defaultValue: 'ec2-nginx', description: 'EC2 instance name',)
        choice(name: 'action', description: '', choices: ['apply' , 'destroy'])
    }
   
    
        stages{
            stage("Repo Setup"){
                steps{
                    checkout scm
                }
                }
             stage('OS Provision') {
                  steps{
                    sh """
                    export TF_VAR_ami_linux=${ami_linux}
                    export TF_VAR_ec2_instance_type1=${ec2_instance_type1}
                    export TF_VAR_key_name=${key_name}
                    export TF_VAR_ec2_instance_name=${ec2_instance_name}
                    export TF_VAR_public_key='${public_key}'
                    
                    """
                  }
            }
        
        stage("terraform setup"){
                         steps{
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'cred_123',ACCESS_KEY: 'ACCESS_KEY', SECRET_KEY: 'SECRET_KEY']])
              {
                                script {
                    def access_key_s3 = AWS_ACCESS_KEY_ID
                    return access_key_s3
                    env.secret_key_s3 = AWS_SECRET_ACCESS_KEY
                                }


                 sh """
                    export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    echo $AWS_ACCESS_KEY_ID
                    export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    export AWS_PROFILE="default"
                    export TF_VAR_access_key=${access_key}
                    export TF_VAR_secret_key=${secret_key}
                    terraform init -reconfigure -no-color
                    """  
                    
                     }
                 
                }
            }
            stage("terraform plan"){
                steps{
                    echo "${access_key_s3}"  
                                             
                     sh """
                        export AWS_ACCESS_KEY_ID=$access_key_s3
                        export AWS_SECRET_ACCESS_KEY=${env.secret_key_s3}

                    sh("terraform plan -no-color")
                }
            }
            stage("terraform action"){
                steps{
                      echo "Terraform action is --> ${action}"
                      sh("terraform ${action} --auto-approve -no-color")
                }
            }
        }
    }
