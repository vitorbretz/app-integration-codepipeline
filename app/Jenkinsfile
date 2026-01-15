// Uses Declarative syntax to run commands inside a container.
pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: python
    image: python:3.9.12-alpine3.15
    command:
    - sleep
    args:
    - infinity
  hostAliases:
  - ip: "172.18.0.50"
    hostnames:
    - "gitea.localhost.com"  
    securityContext:
      # ubuntu runs as root by default, it is recommended or even mandatory in some environments (such as pod security admission "restricted") to run as a non-root user.
      runAsUser: 1000
'''
            // Can also wrap individual steps:
            // container('shell') {
            //     sh 'hostname'
            // }
            // defaultContainer 'shell'
            // retries 2
        }
    }
    stages {
        stage('Unit tests') {
            steps {
                container('python'){
                    sh '''
                        pip install -r app/requirements
                        bandit -r . -x '/.venv/','/tests/'
                        black .
                        flake8 . --exclude .venv
                        pytest -v --disable-warnings
                    '''
                }
            }
        
        }
    }
}
