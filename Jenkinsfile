node('docker') {

    stage('Cleanup') {
        step([$class: 'WsCleanup'])
    }
    stage('Checkout SCM') {
        checkout scm
    }
    def pythonImage
    stage('build docker image') {
        pythonImage = docker.build("awesome:build")
    }
    stage('Prepare container'){
        pythonImage.inside {
          sh """
            pip install nose coverage nosexcover pylint
          """
        }   
    }
    stage('test') {
        pythonImage.inside {
            sh '. /tmp/venv/bin/activate && python -m nosetests -sv --with-xunit --xunit-file=nosetests.xml --with-xcoverage --xcoverage-file=coverage.xml'
        }
    }
    stage('collect test results') {
        junit 'nosetests.xml'
    }
    stage('Sonar Analysis') { 
        def scannerHome = tool 'ADOP SonarScanner'
         withSonarQubeEnv('ADOP Sonar') {
                    sh "${scannerHome}/bin/sonar-scanner"
            }
        
    }
   

}
