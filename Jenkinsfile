pipeline {
  agent {
    node {
      label 'test'
    }

  }
  stages {
    stage('Compile') {
      steps {
        sh './mvnw clean compile'
      }
    }

    stage('Static Analysis') {
      steps {
        sh '''./mvnw sonar:sonar \\
-Dsonar.host.url=http://172.31.19.214:9000/ \\
-Dsonar.projectKey=Petclinic \\
-Dsonar.login=sqp_904783c65b89ff074526307e0921202a425d3014'''
      }
    }

    stage('Unit Test') {
      steps {
        sh './mvnw "-Dtest=**/petclinic/*/*.java" test'
      }
    }

    stage('Package') {
      steps {
        sh './mvnw package -DskipTests=true'
      }
    }

    stage('Deploy') {
      parallel {
        stage('Deploy') {
          steps {
            sh './mvnw spring-boot:run </dev/null &>/dev/null &'
          }
        }

        stage('Integration and Performance Test') {
          steps {
            sh './mvnw verify'
            junit '**/target/surefire-reports/*.xml'
          }
        }

      }
    }

  }
}