pipeline {
  agent any
  environment {
    DOCKERHUB_CREDENTIALS=credentials('dockerhub')
  }
   stages {
    stage ('Build') {
      steps {
        dir ('dockerize_gunicorn-flask') {
            sh '''#!/bin/bash
            python3 -m venv test3
            source test3/bin/activate
            pip install pip --upgrade
            pip install -r requirements.txt
            export FLASK_APP=application
            flask run &
            '''
        }
      }
    }
    stage ('Test') {
      steps {
        dir ('dockerize_gunicorn-flask') {
            sh '''#!/bin/bash
            source test3/bin/activate
            py.test --verbose --junit-xml test-reports/results.xml
            '''
        } 
      }
    }
    stage ('Create Container') {
      agent { label 'dockerDeploy' }
      steps {
        sh 'docker-compose build'
      }
    }
    stage ('Push to DockerHub') {
      agent { label 'dockerDeploy' }
      steps {
        sh '''#!/bin/bash
        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker tag kura_deployment_5_main_nginx:latest ch316/kura_deployment_5_main_nginx:latest
        docker tag kura_deployment_5_main_gunicorn-flask:latest ch316/kura_deployment_5_main_gunicorn-flask:latest
        docker push ch316/kura_deployment_5_main_nginx:latest
        docker push ch316/kura_deployment_5_main_gunicorn-flask:latest
        docker logout
        '''
      }
    }
    stage ('Deploy to ECS') {
      agent { label 'tfDeploy' }
      steps {
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY', variable: 'aws_access_key'), 
                        string(credentialsId: 'AWS_SECRET_KEY', variable: 'aws_secret_key')]) {
                          dir('terraform_ECS_infra') {
                            sh ''' #!/bin/bash
                            terraform init
                            terraform plan -out plan.tfplan -var="aws_access_key=$aws_access_key" -var="aws_secret_key=$aws_secret_key"
                            terraform apply plan.tfplan
                            sleep 60
                            '''
                          }
                        }
      }
    }
    stage ('Destroy ECS Infra') {
      agent { label 'tfDeploy' }
      steps {
        withCredentials([string(credentialsId: 'AWS_ACCESS_KEY', variable: 'aws_access_key'), 
                        string(credentialsId: 'AWS_SECRET_KEY', variable: 'aws_secret_key')]) {
                          dir('terraform_ECS_infra') {
                            sh 'terraform destroy --auto-approve -var="aws_access_key=$aws_access_key" -var="aws_secret_key=$aws_secret_key"'
                          }
                        }
      }
    }
  }
}