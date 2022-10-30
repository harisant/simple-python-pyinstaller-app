pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
		stage('Deploy') {
            agent {
                docker {
					image 'cdrx/pyinstaller-linux'
					args '--entrypoint="" -u root:root'
                }
            }
            steps {
				sh 'apt-get update'
				sh 'apt-get install python-pip -y'
				sh 'python -m pip install --user pyinstaller -y'
				sh 'python -m pyinstaller --onefile sources/add2vals.py'
				sh 'sleep 1'
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
