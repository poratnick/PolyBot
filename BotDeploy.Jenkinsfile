pipeline {
    agent any

    parameters {
            string(name: 'BOT_IMAGE_NAME')
            string(name: 'BOT_IMAGE_NAME2')
    }

    stages {
        stage("Install Ansible") {
            steps {
                sh 'python3 -m pip install ansible'
                sh '/var/lib/jenkins/.local/bin/ansible-galaxy collection install community.general'
            }
        }

        stage("Generate Ansible Inventory") {
            environment {
                BOT_EC2_APP_TAG = "AlonitBot"
                BOT_EC2_REGION = "eu-north-1"
            }
            steps {
                sh 'aws ec2 describe-instances --region $BOT_EC2_REGION --filters "Name=tag:App,Values=$BOT_EC2_APP_TAG" --query "Reservations[].Instances[]" > hosts.json'
                sh 'python3 prepare_ansible_inv.py'
                sh '''
                echo "Inventory generated"
                cat hosts
                '''
            }
        }
    }
}