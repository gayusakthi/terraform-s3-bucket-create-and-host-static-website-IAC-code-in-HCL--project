pipeline {
    agent any

    stages {

        stage('Pull Code from GitHub') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/gayusakthi/terraform-s3-bucket-create-and-host-static-website-IAC-code-in-HCL--project.git'
            }
        }

        stage('Terraform Init, Validate, Plan and Apply') {
            steps {
                sh '''
                    set -e

                    terraform init
                    terraform validate
                    terraform plan
                    terraform apply -auto-approve
                '''
            }
        }

        stage('Upload Website Files to S3') {
            steps {
                sh '''
                    set -e

                    BUCKET_NAME=$(terraform output -raw name | cut -d'.' -f1)

                    aws s3 sync ./ s3://$BUCKET_NAME \
                        --exclude ".git/*" \
                        --exclude ".terraform/*" \
                        --exclude "*.tf" \
                        --exclude "*.hcl" \
                        --exclude "Jenkinsfile" \
                        --exclude "*.md"
                '''
            }
        }
    }

    post {
        success {
            echo 'Static website deployment successful'

            sh '''
                echo "Website Bucket:"
                terraform output -raw name
            '''
        }

        failure {
            echo 'Static website deployment failed'
        }

        always {
            cleanWs()
        }
    }
}
