// def gv

// pipeline {   
//     agent any
//     tools {
//         maven 'Maven'
//     }
//     stages {
//         stage("init") {
//             steps {
//                 script {
//                     gv = load "script.groovy"
//                 }
//             }
//         }
//         stage("build jar") {
//             steps {
//                 script {
//                     gv.buildJar()

//                 }
//             }
//         }

//         stage("build image") {
//             steps {
//                 script {
//                     gv.buildImage()
//                 }
//             }
//         }

//         stage("deploy") {
//             steps {
//                 script {
//                     gv.deployApp()
//                 }
//             }
//         }               
//     }
// } 

pipeline {
    agent any
    tools {
        maven "maven-3.9.14"
    }

    triggers {
        githubPush()
    }

    stages {
        stage("build jar") {
            steps {
                echo 'building the application...'
                sh 'mvn package'
            }
        }

        stage("push image") {
            steps {
                echo "building the docker image..."
                script {
                    def result = input(
                        message: "TYPE VERSION",
                        parameters: [
                            string(name: 'VERSION', defaultValue: 'latest', description: 'version image')
                        ]
                    )

                    withCredentials([usernamePassword(credentialsId: 'docker-credential', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh "docker build -t thaiduong1705/java-maven-app:${result} ."
                        sh "echo $PASS | docker login -u $USER --password-stdin"
                        sh "docker push thaiduong1705/java-maven-app:${result}"
                    }
                }
            }
        }

        stage("deploy") {
            steps {
                echo 'deploying the application...'
            }
        }
    }

    post {
            always {
                cleanWs()
            }

            success {
                echo "Build successful!"
            }

            failure {
                echo "Build failed!"
            }
    }
}