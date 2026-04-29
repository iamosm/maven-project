pipeline {
    agent {
        label 'DevServer'
    }

    parameters {
        choice choices: ['dev', 'prod'], name: 'select_environment'
    }

    environment {
        NAME = "Shariq"
    }

    tools {
        maven 'mymaven'
    }

    stages {
        stage('build') {
            steps {
                script {
                    // Added 'def' to fix the memory leak warning
                    def scriptFile = load "script.groovy"
                    scriptFile.hello()
                }
                sh 'mvn clean package -DskipTests=true'
                
                // Stash here to ensure artifacts are saved immediately after build
                dir("webapp/target/") {
                    stash name: "maven-build", includes: "webapp.war"
                }
            }
        }

        stage('test') { 
            parallel {
                stage('testA') {
                    agent { label 'DevServer' }
                    steps {
                        echo "Running Test Suite A"
                        sh "mvn test"
                    }
                }
                stage('testB') {
                    agent { label 'DevServer' }
                    steps {
                        echo "Running Test Suite B"
                        sh "mvn test"
                    }
                }
            }
        }

        stage('deploy_dev') {
            when { 
                expression { params.select_environment == 'dev' }
                beforeAgent true 
            }
            agent { label 'DevServer' }
            steps {
                dir("/var/www/html") {
                    deleteDir() // Clean directory before extracting new war
                    unstash "maven-build"
                    sh "jar -xvf webapp.war"
                }
            }
        }

        stage('deploy_prod') {
            when { 
                expression { params.select_environment == 'prod' }
                beforeAgent true 
            }
            // Ensure 'ProdServer' is configured in your Jenkins Nodes
            agent { label 'ProdServer' } 
            steps {
                timeout(time: 5, unit: 'DAYS') {
                    input message: "Approve deployment to Production for ${NAME}?"
                }
                dir("/var/www/html") {
                    deleteDir()
                    unstash "maven-build"
                    sh "jar -xvf webapp.war"
                }
            }  
        }
    }
}
