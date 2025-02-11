pipeline {
    agent any  // Runs on Jenkins master node

    parameters {
        string(name: 'AWS_REGION', defaultValue: 'us-east-1', description: 'AWS Region (e.g., us-east-1)')
        string(name: 'VPC_ID', description: 'VPC ID where the EC2 instance will be created')
        string(name: 'SUBNET_ID', description: 'Subnet ID where the EC2 instance will be launched')
        string(name: 'KEY_NAME', description: 'EC2 Key Pair Name for SSH access')
    }

    environment {
        AWS_REGION = "${params.AWS_REGION}"
        VPC_ID = "${params.VPC_ID}"
        SUBNET_ID = "${params.SUBNET_ID}"
        KEY_NAME = "${params.KEY_NAME}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/shivanibhukya/flotorch.git'
                }
            }
        }

        stage('Deploy CloudFormation Stack') {
            steps {
                script {
                    sh '''
                    echo "Starting CloudFormation Stack Deployment..."

                    aws cloudformation deploy \
                        --stack-name MyEC2Stack-${AWS_REGION} \
                        --template-file .github/cloudformation/ec2-template.yml \
                        --parameter-overrides \
                            InstanceType=t2.micro \
                            KeyName=${KEY_NAME} \
                            VpcId=${VPC_ID} \
                            SubnetId=${SUBNET_ID} \
                        --capabilities CAPABILITY_NAMED_IAM

                    echo "✅ CloudFormation Stack Deployment Successful!"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline Completed Successfully!"
        }
        failure {
            echo "❌ Pipeline Failed! Check logs for details."
        }
    }
}
