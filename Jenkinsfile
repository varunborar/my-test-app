podTemplate(yaml: '''
apiVersion: v1
kind: Pod
spec:
  containers:
    - name: node
      image: node:slim
      imagePullPolicy: Always
      tty: true
      
    - name: common
      image: bitnami/git
      imagePullPolicy: Always
      tty: true
      
    - name: docker
      image: docker
      ImagePullPolicy: Always
      command:
        - /bin/cat
      tty: true
      volumeMounts:
        - name: dind-certs
          mountPath: /certs
      env:
        - name: DOCKER_TLS_CERTDIR
          value: /certs
        - name: DOCKER_CERT_PATH
          value: /certs
        - name: DOCKER_TLS_VERIFY
          value: 1
        - name: DOCKER_HOST
          value: tcp://localhost:2376
        
    - name: dind
      image: docker:dind
      securityContext:
        privileged: true
      env:
        - name: DOCKER_TLS_CERTDIR
          value: /certs
      volumeMounts:
        - name: dind-storage
          mountPath: /var/lib/docker
        - name: dind-certs
          mountPath: /certs/client
  volumes:
    - name: dind-storage
      emptyDir: {}
    - name: dind-certs
      emptyDir: {}
''') {

    node(POD_LABEL) {
        stage("Cloning Git Repository") {
            container('common'){
                sh '''
                    git clone https://github.com/varunborar/my-test-app .
                '''
            }
        }
        try{
            stage('Running NPM Install'){
                container('node'){
                    sh '''
                        npm install
                    '''
                }
            }
        }
        catch(e){
            throw(e)
        }
        try{
            stage('Building Docker Image'){
                container('docker'){
                    sh '''
                        docker build -t varunborar/my-test-app:latest .
                    '''
                }
            }
        }
        catch(e){
            throw(e)
        }
    }
}