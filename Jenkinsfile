def appname = "hello-newapp"
def repo = "yonatan009"  // Replace with your DockerHub username
def appimage = "${repo}/${appname}"
def apptag = "${env.BUILD_NUMBER}"

podTemplate(
    containers: [
        containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
        containerTemplate(name: 'docker', image: 'gcr.io/kaniko-project/executor:v1.23.0-debug', command: '/busybox/cat', ttyEnabled: true)
    ],
    volumes: [
        configMapVolume(mountPath: '/kaniko/.docker/', configMapName: 'docker-cred')
    ]
) {
    node(POD_LABEL) {
        stage('checkout') {
            container('jnlp') {
                sh '/usr/bin/git config --global http.sslVerify false'
                checkout scm
            }
        } // end checkout

        stage('build with kaniko') {
            container('docker') {
                sh '''
                /kaniko/executor \
                  --context ${WORKSPACE}/ \
                  --dockerfile ${WORKSPACE}/Dockerfile \
                  --destination docker.io/yonatan009/hello-newapp:${BUILD_NUMBER} \
                  --cleanup
                '''
            }
        } // end build

        stage('verify') {
            echo "✅ Docker image built and pushed successfully: docker.io/${appimage}:${apptag}"
        }
    }
}