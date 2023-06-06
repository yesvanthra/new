node {
    stage('Checkout') {
        git branch: 'master', url: 'https://github.com/yesvanthra/pipeline.git'
    }

    stage('Build') {
        sh 'pip install -r requirements.txt'
        sh 'cd examples && python main.py build'
        sh 'cd fastvector && python vector.py build'
        sh 'cd tests && python test_vector.py build'
    }

    stage('Unit Tests') {
        sh 'pytest --junitxml=test-results.xml'
        junit 'test-results.xml'
    }

    stage('Coverage Report') {
        sh 'pytest --cov=fastvector --cov-report=xml'
        archiveArtifacts artifacts: 'coverage.xml', allowEmptyArchive: true
        step([$class: 'CoberturaPublisher', coberturaReportFile: 'coverage.xml', autoUpdateHealth: false, autoUpdateStability: false])
    }

    stage('SonarQube Analysis') {
        withSonarQubeEnv('sonar-scanner') {
            sh '/opt/sonar-scanner/bin/sonar-scanner ' +
                    '-Dsonar.projectKey=final ' +
                    '-Dsonar.sources=. ' +
                    '-Dsonar.language=py ' +
                    '-Dsonar.sourceEncoding=UTF-8 ' +
                    '-Dsonar.projectVersion=1.0 ' +
                    '-Dsonar.host.url=http://172.18.0.3:9000 ' +
                    '-Dsonar.python.junit.reportPaths=test-results.xml ' +
                    '-Dsonar.python.coverage.reportPaths=coverage.xml ' +
                    '-Dsonar.token='
        }
    }
}
