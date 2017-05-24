pipeline {
  agent any
  stages {
    stage('Echo') {
      steps {
        echo 'Hello World'
      }
    }
    stage('Build') {
      steps {
        dir(path: 'blog') {
          sh 'composer install'
          sh 'cp .env.example .env'
          sh 'php artisan key:generate'
        }
        
        sh '''rm -rf build/coverage build/logs
mkdir build/coverage build/logs'''
      }
    }
    stage('Test') {
      steps {
        parallel(
          "PHPUnit": {
            dir(path: 'blog') {
              sh './vendor/bin/phpunit --coverage-html ../build/coverage --coverage-clover ../build/logs/clover.xml'
            }
            
            junit './build/logs/pmd.xml'
            
          },
          "PHPMD": {
            dir(path: './blog') {
              sh './vendor/bin/phpmd ./app xml ../build/phpmd.xml --reportfile ../build/logs/pmd.xml'
            }
            
            
          }
        )
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy'
        pmd(pattern: 'build/logs/pmd.xml')
        sh 'pwd'
      }
    }
  }
}