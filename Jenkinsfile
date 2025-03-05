node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
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
    
    stage('Deliver') {
        docker.image('cdrx/pyinstaller-linux:python2').inside {
            sh 'pyinstaller --onefile sources/add2vals.py'
        }
        archiveArtifacts 'dist/add2vals'
    }
    
    stage('Manual Approval') {
        script {
            def userInput = input(message: "Lanjutkan ke tahap Deploy?", ok: "Proceed")
        }
    }
    
    stage('Deploy') {
        def ec2_user = 'ec2-user'
        def ec2_host = '54.251.238.140'
        def pem_key = '~/key-pair/python-app.pem'
        
        sh "scp -i ${pem_key} dist/add2vals ${ec2_user}@${ec2_host}:/opt/application/"
        sh "ssh -i ${pem_key} ${ec2_user}@${ec2_host} 'chmod +x /opt/application/add2vals && sudo systemctl restart app-service'"
        
        echo "Aplikasi berjalan selama 1 menit sebelum pipeline selesai..."
        sh "sleep 60"
    }
    
    stage('Monitoring Setup') {
        sh "echo 'Setting up Prometheus and Grafana with custom configurations'"
        sh "docker run -d --name prometheus -p 9090:9090 -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus --web.listen-address=0.0.0.0:9090 --web.telemetry-path=/prom"
        sh "docker run -d --name grafana -p 3000:3000 grafana/grafana"
    }
}