```groovy
pipeline {
    agent any

    stages {

        stage('pull code from github') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/gayusakthi/terraform-s3-bucket-create-and-host-static-website-IAC-code-in-HCL--project.git'
            }
        }

        stage('terraform init and apply') {
            steps {
                sh 'terraform init'
                sh 'terraform validate'
                sh 'terraform plan'
                sh 'terraform apply -auto-approve'
            }
        }

        stage('upload files to s3 bucket') {
            steps {
                sh '''
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
            echo 'static website deployment successful'
            sh 'terraform output -raw name'
        }

        failure {
            echo 'static website deployment failure'
        }
    }
}
```
