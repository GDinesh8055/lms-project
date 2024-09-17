pipeline {
agent any
stages {
    stage('LMS code analysis')  {
        steps{ 
            echo 'sonar code analysis started'
            sh 'cd webapp && sudo docker run --rm -e SONAR_HOST_URL="http://54.165.234.35:9000" -v ".:/usr/src" -e SONAR_TOKEN="sqp_a1fbea412a8b4fd79ea8c574de4cb5c1fef40220" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
            echo 'sonar code analysis completed'
        }
    }
    stage('LMS build'){
        steps{
            echo 'LMS build started'
            sh 'cd webapp && npm install && npm run build'
            echo 'LMS build completed'
        }
    }
    stage('LMS release'){
        steps{
            script {
                def packageJson = readJSON file: 'webapp/package.json'
                def packageJSONVersion = packageJson.version
                echo "${packageJSONVersion}"
                sh "zip webapp/lms-${packageJSONversion}.zip -r webapp/dist"
                sh "curl -v -u admin:lms12345 --upload-file webapp/lms-${packageJSONVersion}.zip http://54.165.234.35:8081/repository/lms/"
            }
            
        }
    }
    stage('LMS Deploy') {
        steps {
            script {
                def packageJson = readJSON file: 'webapp/package.json'
                def packageJSONversion = packageJson.version
                echo "${packageJSONversion}"
                sh  "curl -u admin:lms12345 -X GET \'http://54.165.234.35:8081/repository/lms/lms-${packageJSONVersion}.zip\' --output lms-'${packageJSONVersion}'.zip"
                sh "sudo rm -rf /var/www/html/*"
                sh "sudo unzip -o lms-'${packageJSONversion}'.zip "
                sh "sudo cp -r webapp/dist/* /var/www/html"
            }
        }
    }
    stage('clean up workspace') {
        steps {
            echo 'cleanup WorkSpace'
            cleanWs()
        }
    }
}
}