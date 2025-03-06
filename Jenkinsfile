node {
    stage('Build') {
        docker.image('python:3.9').inside('-u 0') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside('-u 0') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
    }

    stage('Publish Test Results') {
        junit 'test-reports/results.xml'
    }

    stage('Approval') {
        script {
            def userInput = input(message: "Lanjutkan ke tahap Deploy?", ok: "Proceed")
        }
    }

    stage('Deploy') {
        docker.image('python:3.9').inside('-u root') {
            sh '''
                pip install pyinstaller
                pyinstaller --onefile sources/add2vals.py
            '''
            sleep 60  // Tunggu 1 menit untuk memastikan build selesai
            echo 'Pipeline has finished successfully.'
        }
    }

    archiveArtifacts 'dist/add2vals'
}
