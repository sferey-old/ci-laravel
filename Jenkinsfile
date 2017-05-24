pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''rm -rf build/coverage build/logs
mkdir build/coverage build/logs'''
        sh 'composer install'
        sh 'cp .env.example .env'
        sh '''php artisan key:generate
'''
      }
    }
    stage('Test') {
      steps {
        parallel(
          "PHPUnit": {
            sh './vendor/bin/phpunit --coverage-html ../build/coverage --coverage-clover ../build/logs/clover.xml'
            
          },
          "PHPMD": {
            sh './vendor/bin/phpmd ./app xml ../build/phpmd.xml --reportfile ../build/logs/pmd.xml'
            
          }
        )
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy'
        pmd(pattern: 'build/logs/pmd.xml')
      }
    }
  }
}