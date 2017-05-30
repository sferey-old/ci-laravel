pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''rm -rf build/coverage build/logs
mkdir build/coverage build/logs'''
        sh 'composer update'
        sh 'cp .env.example .env'
        sh '''php artisan key:generate
'''
      }
    }
    stage('Test') {
      steps {
        parallel(
          "PHPUnit": {
            sh './vendor/bin/phpunit --log-junit ../build/logs/phpunit.xml'
            
          },
          "PHPMD": {
            sh './vendor/bin/phpmd ./app xml ./build/phpmd.xml --reportfile ./build/logs/pmd.xml'
            
          }
        )
      }
    }
    stage('Rapport') {
      steps {
        pmd(pattern: 'build/logs/pmd.xml')
        junit 'build/logs/phpunit.xml'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy'
        emailext(subject: '[JENKINS] CI-LARAVEL', body: 'Ready to deploy', to: 's.ferey@cleo-consulting.fr')
      }
    }
  }
}