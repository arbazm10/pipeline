CODE_CHANGES = getGitChanges
pipeline {
    agent node-01
    environment {
        NEW_VERSION = '1.3.0'
        SERVER_CREDENTIALS = credentials('arbazsecret') #credentials create and add here add credentials and credentails binding plugin
    }
    tools{
        maven 'Maven'
    }
    parameters{
        string(name: ''VERSION, defaultValue: '', description: '')
        choices(name: 'VERSION', choices: ['1.0','1.1','1.2'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description:'') 
    }
    stages {
        stage ("sc pull") {
            steps { 
                git url
            }
        }  
        stage ("test") {
            when {
                expression {
                    BRANCH_NAME == 'dev' || BRANCH_NAME == 'master' || CODE_CHANGES == true
                }
            }
            when {
                expression {
                    params.executeTests == true
                }
            }
            steps {   
                sh "docker build -t arbaz ."
                sh " docker tag arbaz arbazm10/latest"
                echo "building version ${NEW_VERSION}"
            }
        }
        stage ("image push") {
            steps {   
            }
        }
        stage ("deploy") {
            steps {   
                echo "deploying with ${SERVER_CREDENTIALS}"
                or 
                with credentails([
                    usernamePassword(credentials: 'arbazsecret', usernamevariable: USER, passwordVariable: PWD)

                ]){
                    sh "docker run -itd arbazm10/arbaz:latest ${USER} ${PWD}"
                    echo "deploying version ${params.VERSION}"
                }
            }
        }
    }
        post {
        // Clean after build
        always {
            cleanWs()
        }
    }

}
