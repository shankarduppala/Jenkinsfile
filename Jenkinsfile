pipeline {
  agent any
  paramers {
    string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git branch to build')
    booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run unit test')
    choices(name: 'DEPLOY_ENV', choices: ['dev", 'qa', 'prod'], description: 'select deployment environment')
  }                                       
  environment {
    MAVEN_OPTS = '-Xmxhdeheh'
    DEPLOY_SCRIPT = './scripts/peploy.sh'
  }
