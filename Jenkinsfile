pipeline { 
    agent any
    
    tools {
        maven 'maven3'
        jdk 'jdk21'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

parameters {
        booleanParam(name: 'RUN_GIT_CHECKOUT', defaultValue: true, description: 'Run Git checkout stage')
        booleanParam(name: 'RUN_MVN_COMPILE', defaultValue: true, description: 'Run MVN Compile stage')
        booleanParam(name: 'RUN_MVN_TEST', defaultValue: true, description: 'Run MVN Test stage')
        booleanParam(name: 'RUN_MVN_PACKAGE', defaultValue: true, description: 'Run MVN package stage')
        booleanParam(name: 'RUN_NEXUS_UPLOAD', defaultValue: true, description: 'Run Nexus_upload stage')
       // booleanParam(name: 'RUN_GIT_LEAKS', defaultValue: true, description: 'Run GitLeaks scan?')
        booleanParam(name: 'RUN_SONAR', defaultValue: true, description: 'Run SonarQube analysis?')
        booleanParam(name: 'RUN_OWASP', defaultValue: true, description: 'Run OWASP scan?')
        booleanParam(name: 'RUN_DOCKER_BUILD', defaultValue: true, description: 'Build and push Docker images?')
        booleanParam(name: 'RUN_TRIVY', defaultValue: true, description: 'Run Trivy scan?')
        booleanParam(name: 'RUN_DEPLOY_CONTAINER', defaultValue: true, description: 'Run Deploy to container?')
    }
    stages {

        stage('Git checkout') {
            when { expression { params.RUN_GIT_CHECKOUT } } 
            steps {
           git branch: 'master', credentialsId: 'github-login', url: 'https://github.com/ashu290996/FullStack-Blogging-App-main.git'
            }
        }

        stage('MVN compile') {
            when { expression { params.RUN_MVN_COMPILE } }
            steps {
                sh "mvn compile"
            }
        }

        stage('MVN test') {
            when { expression { params.RUN_MVN_TEST } }
            steps {
                sh "mvn test"
            }
        }
        stage('MVN package') {
            when { expression { params.RUN_MVN_PACKAGE } }
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
         
        stage('Nexus Upload') {
            when { expression { params.RUN_NEXUS_UPLOAD } }
            steps {
                withMaven(globalMavenSettingsConfig: 'Global-maven-setting', jdk: 'jdk21', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                sh "mvn deploy -DskipTests=true" }
            }
        }
        stage('Sonar Analysis') {
            when { expression { params.RUN_SONAR } }
            steps {
                withSonarQubeEnv('sonar-scanner') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=FullStack-Blogging-App \
                        -Dsonar.projectKey=FullStack-Blogging-App
                    '''
                }
            }
        }
        stage('OWASP SCAN') {
            when { expression { params.RUN_OWASP } }
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP-check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
       
    }
}
