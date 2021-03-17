podTemplate(label: 'jenkins-slave-pod', 
  containers: [
    containerTemplate(
      name: 'git',
      image: 'alpine/git',
      command: 'cat',
      ttyEnabled: true
    ),
    containerTemplate(
      name: 'maven',
      image: 'maven:3.6.2-jdk-8',
      command: 'cat',
      ttyEnabled: true
    ),
    containerTemplate(
      name: 'node',
      image: 'node:8.16.2-alpine3.10',
      command: 'cat',
      ttyEnabled: true
    ),
    containerTemplate(
      name: 'docker',
      image: 'docker',
      command: 'cat',
      ttyEnabled: true
    ),
  ],
  volumes: [ 
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), 
  ]
)

{
    node('jenkins-slave-pod') { 
        def registry = "192.168.194.129:5000"
        def registryCredential = "private-repository-id"

        stage('Clone repository') {
            container('git') {
                // https://gitlab.com/gitlab-org/gitlab-foss/issues/38910
                checkout([$class: 'GitSCM',
                    // branches: [[name: '*/dockerizing']],
                    branches: [[name: ]],
                    userRemoteConfigs: [
                        [url: 'https://github.com/govy68/T-jenkins-github-1.git', credentialsId: 'github_ansible-in-action']
                    ],
                ])
            }
        }

/*        stage('build the source code via npm') {
            container('node') {
                sh 'cd frontend &amp;&amp; npm install &amp;&amp; npm run build:production'
            }
        }

        stage('build the source code via maven') {
            container('maven') {
                sh 'mvn package'
                sh 'bash build.sh'
            }
        }
*/
        stage('Build docker image') {
            container('docker') {
                withDockerRegistry([ credentialsId: "$registryCredential", url: "http://$registry" ]) {
                    sh "docker build -t $registry/stlapp:1.0 -f ./Dockerfile ."
                }
            }
        }

        stage('Push docker image') {
            container('docker') {
                withDockerRegistry([ credentialsId: "$registryCredential", url: "http://$registry" ]) {
                    docker.image("$registry/stlapp:1.0").push()
                }
            }
        }
    }   
}
