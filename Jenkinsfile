node {
	stage('Build') {
		withDockerContainer(image: 'python:2-alpine') {
			sh 'python -m py_compile sources/add2vals.py sources/calc.py'
			stash(name: 'compiled-results', includes: 'sources/*.py*')
		}
	}
	
	stage('Test') {
		withDockerContainer(image: 'qnib/pytest') {
			sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
		}
			junit 'test-reports/results.xml'
	}
	withEnv(['VOLUME=$(pwd)/sources:/src','IMAGE=cdrx/pyinstaller-linux:python2']) {
		stage('Deploy) {
			dir(path: env.BUILD_ID) { 
				unstash(name: 'compiled-results') 
				sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" 
				}
			}
			archiveArtifacts "${env.BUILD_ID}/sources/dist/add2vals" 
			sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'"
		}
	}
}
