node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }
    
    stage('Publish test results') {
        junit 'test-reports/results.xml'
    }
    
    stage('Manual Approval') {
        script {
            def userInput = input(message: "Lanjutkan ke tahap Deploy?", ok: "Proceed")
        }
    }

    stage('Deploy') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            sh 'pyinstaller --onefile sources/add2vals.py'
            echo 'Pipeline has finished successfully.'
        }
        
        post {
            success {
                archiveArtifacts 'dist/add2vals'
            }
        }
    }
}
