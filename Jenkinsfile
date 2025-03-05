node {
    stage('Build') {
        docker.image('python:2-alpine').inside('-u 0') {  // Menjalankan container sebagai root
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    
    stage('Test') {
        docker.image('qnib/pytest').inside('-u 0') {
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
        docker.image('python:2-alpine').inside('-u 0') {  // Pastikan dijalankan sebagai root
            sh '''
                apk add --no-cache py-pip
                pip install pyinstaller
                pyinstaller --onefile sources/add2vals.py
            '''
            echo 'Pipeline has finished successfully.'
        }
    }

    archiveArtifacts 'dist/add2vals'
}
