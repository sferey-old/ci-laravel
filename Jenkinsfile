pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'rm -rf build/coverage build/logs'
        sh 'mkdir build/coverage build/logs'
        sh 'composer update'
        sh 'cp .env.example .env'
        sh 'php artisan key:generate'
      }
    }
    stage('Test') {
      steps {
        parallel(
          "PHPUnit": {
            sh './vendor/bin/phpunit'
            junit 'build/logs/junit.xml'
            
          },
          "PHPMD": {
            sh './vendor/bin/phpmd ./app xml ./build/phpmd.xml --reportfile ./build/logs/pmd.xml'
            pmd(pattern: 'build/logs/pmd.xml')
            
          },
          "PHPCode_Sniffer": {
            sh './vendor/bin/phpcs'
            
          },
          "PHPLOC": {
            sh './vendor/bin/phploc --log-csv build/logs/phploc.csv --log-xml build/logs/phploc.xml app'
            
          }
        )
      }
    }
    stage('Documentation') {
      steps {
        sh './vendor/bin/phpdox --file build/phpdox.xml'
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deploy'
        emailext(subject: '[JENKINS] CI-LARAVEL', body: 'Ready to deploy', to: 's.ferey@cleo-consulting.fr')
        mail(subject: 'Deploy', body: 'Content Deploy', to: 's.ferey@cleo-consulting.fr')
      }
    }
  }
}