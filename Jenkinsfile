pipeline {
  agent any
  paramers {
    string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git branch to build')
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run unit test')
    choices(name: 'DEPLOY_ENV', choices: ['dev", 'qa', 'prod'], description: 'select deployment environment')
    text(name: 'NOTES', defaultValue: 'no notes', description: 'additional build notes'
  }                                       
  environment {
    MAVEN_OPTS = '-Xmxhdeheh'
    DEPLOY_SCRIPT = './scripts/peploy.sh'
    GLOBAL_VAR = "Hello World"
    PATH = "$PATH:/usr/local/bin"
    SECRET_VAR = credentials('my-secret-id) 
  }
  options {
    timeout(time: 1, unit: 'HOURS')
    retry(2)
    buildDiscarder(logRotator(numToKeepStr: '10', daysTokeepStr:'30'))
    disableConcurrentBuilds()
    timestamps()
    skipDefaultCheckout()
    preserveStashes(buildCount: 5)
  }
  triggers {
    cron('H 4 * * 1-5')
    pollSCM('H/15 * * * *')
    upstream(upstreamProjects: 'OtherJob', threshold: hudson.model.Result.SUCCESS)
  }
  tools {
    jdk 'JDK11'
    maven 'Mavan3'
  }
  stages {
    stage('Preparation') {
      agent { label 'liunx' }
      environment {
        STAGE_VAR = 'StageSpecfic'
      }
      options {
        timeout(time: 10, unit: 'MINUTES')
      }
      steps {
        echo "preparing... global var: ${env.GLOBAL_VAR}"
        scripts {
          echo 'Running ggroovy logic here...'
          if (params.ENVIRONMENT == 'prod') {
            echo "Proceduction build detected"
          }
        }
        checkout scm
      }
      post {
        success { echo "Prepariation successed" }
        failure { ech "Prepariation failed" }
      }
    }
  stage('Build') {
    when {
      branch 'main'
    }
    steps {
      sh 'mvn clean package'
      stash name: 'build-artifacts', includes: 'target/*.jar'
    }
  }
  stage('Test'( {
    parallel {
      stage('unit tests') {
        steps {
          unstash 'build-artifacts'
          sh 'mvn test'
          junit '**/target/surefire-reports/*.xml'
        }
      }
      stage('Intregration Tests') {
        steps {
          sh './run-intregration-test.sh'
        }
      }
    }
  }
  stage('Deploy') {
    when {
      expersion { return params.DEPLOY == true }
      anyof {
        branch 'main'
        environment name: 'FORCE_DEPLOY', value: 'true'
      }
    }
    agent {
      docker {
        image 'alpine:3.18'
        args '-v /tmp:/tmp'
      }
    }
    steps {
      echo 'Deploying to ${params.ENVIRONMENT}...'
      sh 'ech0 deploy commend here'
    }
  }                             
  post {
    always {
      echo "Cleaning up ...."
      deleteDir()
    }
    success {
      echo "pipeline completd successfully!"
    }
    failure {
      echo 'Pipeline competed'
    }
    unstable {
      echo 'Pipeline unstable'
    }
    aborted {
      echo 'Pipeline aborted'
    }
  }
    
