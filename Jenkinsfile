pipeline {

    agent none
environment {
    DEPLOY_HOST = "172.31.30.133"
    DEPLOY_USER = "ec2-user"
    TOMCAT_PATH = "/mnt/apache-tomcat-10.1.56/webapps"
}    
    stages {

        stage('Checkout') {
            agent { label 'agent-1' }

            steps {
                git branch: 'master',
                    url: 'https://github.com/swethapujari123/jenkinshttpd.git'
            }
        }

        stage('Build WAR') {
            agent { label 'agent-1' }

            steps {
                sh 'mvn clean package'
            }
        }
        
  stage('Copy WAR to Agent2') {
    agent { label 'agent-1' }

    steps {
        sh '''
        scp -i /home/ec2-user/mykey.pem \
        -o StrictHostKeyChecking=no \
        target/myapp.war \
        ec2-user@172.31.30.133:/tmp/myapp.war
        '''
    }
}        

        stage('Deploy on Tomcat') {
            agent { label 'agent-2' }

            steps {
                sh """
                sudo systemctl stop tomcat

                rm -rf /mnt/apache-tomcat-10.1.56/webapps/
                mv /tmp/myapp.war /mnt/apache-tomcat-10.1.56/webapps/myapp.war

                sudo systemctl start tomcat
                """
            }
        }
    }
}
