//luke  devsecops pipeline
#!/usr/bin/env groovy
pipeline {
  agent any
    // 可以设置环境变量
  tools {
    maven "apache-maven-3.6.3"
  }

  stages {
    stage('Build') {
      steps {
        git 'https://github.com/ajlanghorn/dvja.git'
        sh "mvn clean package"
        echo 'This is the first step!'

      }
    }
    // stage可以添加或减少
    stage('Check dependencies') {
      steps {
        dependencyCheck additionalArguments: '', odcInstallation: 'Dependency-Check'
        dependencyCheckPublisher pattern: ''
       //dependencyCheckPublisher failedTotalCritical: 1, failedTotalHigh: 1, failedTotalLow: 10, failedTotalMedium: 5, pattern: '', unstableTotalCritical: 1, unstableTotalHigh: 1, unstableTotalLow: 10, unstableTotalMedium: 5
       echo 'This is the second step!'

      }
    }
    stage('Scan for vulnerabilities') {
    steps {
    sh 'zap-cli quick-scan --self-contained --spider -r http://127.0.0.1'
    //sh 'java -jar dvja-*.war && zap-cli quick-scan --self-contained --spider -r http://127.0.0.1 && zap-cli report -o zap-report.html -f html'
    echo 'This is the Third step!'
       }
    }
    stage('Analysis') {
      steps {
        sh "mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd spotbugs:spotbugs"
      }
    }
    stage('Publish to S3') {
      steps {
        sh "aws s3 cp /var/lib/jenkins/workspace/dvja/target/dvja-1.0-SNAPSHOT.war s3://ako20-buildartifacts-1voo45fcneh1p/dvja-1.0-SNAPSHOT.war"
      }
    }
    stage('Tidy up') {
      steps {
        cleanWs()
      }
    }
  }
  //post {
  //  always {
  //    archiveArtifacts artifacts: 'zap-report.html', fingerprint: true
  //    recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
  //    recordIssues enabledForFailure: true, tool: checkStyle()
  //    recordIssues enabledForFailure: true, tool: spotBugs()
  //    recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
  //    recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
  //    }
  //}
}
